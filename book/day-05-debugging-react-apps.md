# Day 05: Debugging React Apps

Table of contents

1. [Understanding Error Message](#1-understanding-error-message)
2. [Debug with React Developer Tools](#2-debug-with-react-developer-tools)
3. [Error Boundaries](#3-error-boundaries)

---

## 1. Understanding Error Message

## 2. Debug with React Developer Tools

## 3. Error Boundaries

Ok anh em tiếp theo là error boundaries nhé:

- **Error boundaries** là một React components để bắt tất cả các lỗi Javascript ở bất cứ vị trí nào trong cây component con, log lỗi ra console cũng như là hiển thị lên UI thay vì khiến cho toàn bộ cây component bị crash.

- Error boundaries **chỉ** áp dụng với class component.

- Hoạt động như 1 khối try catch của component.
  Ok dưới đây là mẫu nhé ( lần này mình viết bằng typescript nhé ):

```tsx
// ./component/Error.tsx
import React from "react";

interface IError {
  hasError: boolean;
  errorDescription: string;
}

export default class ErrorBoundary extends React.Component<any, IError> {
  constructor(props: { props: any }) {
    super(props);
    this.state = { hasError: false, errorDescription: "" };
  }

  static getDerivedStateFromError(error: Error) {
    return { hasError: true, errorDescription: error.message };
  }

  componentDidCatch(error: Error, errorInfo: any) {
    // Log ra xem lỗi anh em nhé
    console.log(error);
  }

  render() {
    if (this.state.hasError) {
      // You can render any custom fallback UI
      return <h1> {this.state.errorDescription}</h1>;
    }

    return this.props.children;
  }
}
```

- Block catch error của component với componentDidCatch hoạt đông như khối catch.
- getDerivedStateFromError các bạn có thể không cần mà gán lại state trong componentDidCatch cũng được nhé.

Tiếp theo là component Avenger.

```tsx
// ./components/Avenger.tsx
import React, { FC } from "react";

export enum side {
  Hero = "Hero",
  Villian = "Villian",
}

interface AvengerProps {
  profile: {
    name: string;
    side: side;
  };
}

const Avenger: FC<AvengerProps> = ({ profile }) => {
  if (profile.side === side.Villian) throw new Error("Villian detected");

  return <h1>Hello hero :{profile.name}</h1>;
};

export default Avenger;
```

- App component anh em nhé:

```tsx
import React from "react";
import "./App.css";
import Error from "./components/Error";
import Avenger, { side } from "./components/Avenger";
function App() {
  return (
    <>
      <Error>
        <Avenger profile={{ name: "Steve Rodgers", side: side.Hero }} />
      </Error>
      <Error>
        <Avenger profile={{ name: "Thanos", side: side.Villian }} />
      </Error>
      <Error>
        <Avenger profile={{ name: "Ebony Maw", side: side.Villian }} />
      </Error>
    </>
  );
}

export default App;
```

- Nếu các bạn chạy thì bùm đỏ lòm.Ơ sao lại thế vì React team muốn trong lúc development dev sẽ nhìn thấy lỗi một cách rõ ràng nhất.Ok để remove cái màn đỏ lòm lỗi này thì các bạn bấm cái dấu X màu trắng ở góc phải màn hình nha.

- Hừm tại sao trong App component mình không bọc cả vào trong một khối Error mà lại tách ra đó là vì cơ chế của Error boundaries nếu gặp một lỗi nó sẽ display fallback UI trong Error component vì thế nếu để 1 khối thì nếu gặp lỗi bạn sẽ chỉ thấy 1 fallback ui thôi.

+ Link tham khảo : <https://reactjs.org/docs/error-boundaries.html>