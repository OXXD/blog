---
title: 如何实现 React Router 的路由鉴权
description:
date: 2023-11-05
tag: React
---

# 如何实现 React Router 的路由鉴权

[React Router](https://reactrouter.com/en/main) 是 `React` 应用中最常用的路由库之一，路由鉴权是确保应用安全性和数据保护的关键部分。
本文将介绍如何使用 `React Router` 实现路由鉴权，以及如何限制用户访问特定页面，确保只有经过鉴权的用户可以访问（如登录后、购买会员后等条件）。

以官方文档中的 [Auth Example](https://github.com/remix-run/react-router/tree/dev/examples/auth) 代码为例，
在线演示可以见 [StackBlitz](https://stackblitz.com/github/remix-run/react-router/tree/main/examples/auth-router-provider?file=src/App.tsx)

## 1. 定义路由

这里我们定义三个路由，分别为 首页 `/`，登录页 `/login`，需要登录后才能访问的页面 `/protected`

```tsx
export default function App() {
  return (
      <Routes>
        <Route element={<Layout />}>
          <Route path="/" element={<PublicPage />} />
          <Route path="/login" element={<LoginPage />} />
          <Route
            path="/protected"
            element={<ProtectedPage />}
          />
        </Route>
      </Routes>
  );
}

function LoginPage() {
  let navigate = useNavigate();
  let location = useLocation();

  let from = location.state?.from?.pathname || "/";

  function handleSubmit(event: React.FormEvent<HTMLFormElement>) {
    event.preventDefault();

    let formData = new FormData(event.currentTarget);
    let username = formData.get("username") as string;

    signin(username, () => {
      navigate(from, { replace: true });
    });
  }

  return (
    <div>
      <p>You must log in to view the page at {from}</p>

      <form onSubmit={handleSubmit}>
        <label>
          Username: <input name="username" type="text" />
        </label>{" "}
        <button type="submit">Login</button>
      </form>
    </div>
  );
}

function PublicPage() {
  return <h3>Public</h3>;
}

function ProtectedPage() {
  return <h3>Protected</h3>;
}
```

## 2. 实现路由拦截组件

实现路由拦截组件只需要判断用户是否登录即可，也可以按照项目实际需求判断条件。这个组件的作用是用来包裹需要鉴权的路由组件，如果不符合判断条件则跳转到登录页面。

```tsx
function RequireAuth({ children }: { children: JSX.Element }) {
  let auth = useAuth();
  let location = useLocation();
  
  // 这里判断用户是否登录，如果未登录则跳转到登录页面
  if (!auth.user) {
    return <Navigate to="/login" state={{ from: location }} replace />;
  }

  return children;
}
```

判断用户登录官方示例代码中是使用 `Context` 这个 `React` 特性实现的 `AuthProvider` ，便于不同组件获取共用的状态。
我们也可以使用其他符合项目的方式判断用户是否登录，比如将用户登录状态存到全局状态库（如 `Redux`）或者 `localStorage` 中，示例代码如下：

```tsx
const isLogin = useAppSelector((state) => state.user.isLogin);
const userInfo = useAppSelector((state) => state.user.userInfo);
const dispatch = useAppDispatch();

useEffect(() => {
  if (isLogin && !userInfo) {
    dispatch(fetchUserInfo());
  }
}, [dispatch, isLogin, userInfo]);

if (!isLogin) {
  return <Navigate to="/login" state={{ from: location }} replace />;
}
```

## 3. 为需要鉴权的路由增加路由拦截组件包裹

最后只需要为需要鉴权的路由增加路由拦截组件即可，这么实现充分的利用的 `React` 的组合式思想，可以方便的为是否需要鉴权的路由增减鉴权功能（便于配置维护），
也通过封装统一的组件提高了代码的复用，逻辑改变时只需要修改通用组件即可。

```tsx {10,12}
export default function App() {
  return (
      <Routes>
        <Route element={<Layout />}>
          <Route path="/" element={<PublicPage />} />
          <Route path="/login" element={<LoginPage />} />
          <Route
            path="/protected"
            element={
              <RequireAuth>
                <ProtectedPage />
              </RequireAuth>
            }
          />
        </Route>
      </Routes>
  );
}
```

## 参考链接

- [React Router Docs](https://reactrouter.com/en/main)
- [React Router Auth Example](https://github.com/remix-run/react-router/tree/dev/examples/auth)
- [React Router Auth Example 在线演示](https://stackblitz.com/github/remix-run/react-router/tree/main/examples/auth-router-provider?file=src/App.tsx)