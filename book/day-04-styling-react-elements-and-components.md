# Day 04: Styling React Elements and Components

Table of contents

1. [Adding Styling with Stylesheets](#1-adding-styling-with-stylesheets)
2. [Setting Class Name Dynamically](#2-setting-class-name-dynamically)
3. [Working with CSS Modules](#3-working-with-css-modules)
4. [CSS Modules and Media Queries](#4-css-modules-and-media-queries)

5. [Inline Styles](#5-inline-styles)
6. [Setting Styles Dynamically](#5-setting-styles-dynamically)
7. [Additional: Using Radium for Pseudo and Media Queries](#7-additional-using-radium-for-pseudo-and-media-queries)
8. [Additional: Using Styled Components and Dynamic Styles](#8-additional-using-styled-components-and-dynamic-styles)

---

## 1. Adding Styling with Stylesheets

Cách 1 đó là import một file css. Mình tạo mới một file Person.css như sau:

```css
/* ./component/person/Person.css */
.person {
  padding: 20px;
  border: 1px solid #eeeeee;
  border-radius: 4px;
  margin-bottom: 30px;
  width: 60%;
}

.person:hover {
  box-shadow: 0 2px 4px #cccccc;
}
```

Sau đó chúng ta import vào file Person.js rồi dùng như bình thường thôi:

```jsx
import "./Person.css";

const Person = (props) => {
  const clickHandle = () => {
    props.click();
  };

  return (
    <div className="person">
      <p onClick={clickHandle}>I am {props.name}!</p>
      <p>{props.children}</p>
      <input type="text" onChange={props.change} value={props.name} />
    </div>
  );
};

export default Person;
```

## 2. Setting Class Name Dynamically

Giả sử trên file Person.css của chúng ta có thêm 2 style khác

```css
.personBackgroundRed {
  background-color: red;
}
.personBackgroundBlack {
  background-color: black;
}
```

Ở đây chúng ta thêm style vào cho cái div có sẵn class person kia với điều kiện personBackground true hoặc false;
Hoặc các bạn có thể truyền qua props tùy bạn thôi;
Để làm được điều đó chúng ta thay như dưới:

```jsx
import "./Person.css";
const Person = (props) => {
  const [personBackground, setPersonBackground] = useState(true);

  return (
    <div
      className={`person ${
        personBackground ? "personBackgroundRed" : "personBackgroundBlack"
      }`}
    >
      <button onClick={() => setPersonBackground((prev) => !prev)}>
        Change background
      </button>
    </div>
  );
};

export default Person;
```

## 3. Working with CSS Modules

Với CSS Modules thì có những thay đổi như sau

CSS module là những file css bao gồm tất cả các class names và animation names.
Vì vậy, cũng gần giống như một số ngôn ngữ css mở rộng như sass hay scss, css module không thể thực thi trực tiếp trên trình duyệt mà cần thông qua các trình biên dịch ( Webpack hoặc Browserify)

Cú pháp:

- Css module dùng cú pháp của css nhưng có vài điểm khác biệt:

* Tên file css là person.css thì module là person.module.css
* Lúc import vào sẽ khác file css
* Tên class trong devtool sẽ bị đổi

```jsx
import styles from "../css/person.module.css";
const Person = (props) => {
  const clickHandle = () => {
    props.click();
  };

  return (
    <div className={styles.person}>
      <p onClick={clickHandle}>I am {props.name}!</p>
      <p>{props.children}</p>
      <input type="text" onChange={props.change} value={props.name} />
    </div>
  );
};

export default Person;
```

```css
.font {
  font-family: "Open Sans";
}

.person {
  padding: 20px;
  border: 1px solid #eeeeee;
  border-radius: 4px;
  margin-bottom: 30px;
  width: 60%;
  composes: font;
}

.person:hover {
  box-shadow: 0 2px 4px #cccccc;
}
```

Anh em có nhìn thấy điểm gì khác biệt đúng rồi đó là từ khóa composes nó lấy các class khác và gán vào sau khi dùng class person ở bên kia thì mở devtool lên sẽ thấy hai class

**Cách 2 anh em nhé**

- Để sử dụng anh em cài cái này nhé:

* Với js:npm i react-css-modules
* Với typescript:npm i --save-dev @types/react-css-modules

Giả sử mình có 2 module css như sau:

````css
/* person.module.css */
.font-size {
  font-size: 12px;
}
.background {
  background-color: blue;
}
.person {
  padding: 20px;
  border: 1px solid #eeeeee;
  border-radius: 4px;
  margin-bottom: 30px;
  width: 60%;
  composes: font-size;
}

.person:hover {
  box-shadow: 0 2px 4px #cccccc;
}
@media (max-width: 768px) {
  .person {
    font-size: 20px;
  }
}
```

```css
/* font.module.css */
.primary {
  font-family: "Helvetica Neue";
}
````

```jsx
import React, { FC, useState } from "react";
import CSSModules from "react-css-modules";
import styles from "../css/person.module.css";
import font from "../css/font.module.css";

const Person = (props) => {
  return (
    <div styleName="person">
      <p styleName="primary">I am {props.name}!</p>
      <p>{props.children}</p>
      <input type="text" onChange={props.change} value={props.name} />
    </div>
  );
};

export default CSSModules(
  Person,
  { ...font, ...styles },
  { allowMultiple: true }
);
```

**Note:**

- Cái spread ở cuối cùng là để import nhiều styles vào cái component này lưu ý là cái style nào bạn muốn chèn đè lên thì để sau nhé(thử thêm
  .primary{
  font-family: 'Franklin Gothic Medium', 'Arial Narrow', Arial, sans-serif;
  }
  ở person.module.css nhé
  )
- Nếu cái styleName bạn truyền một giá trị mà không có trong style nào được import vào kia thì sẽ **đỏ lòm**

Link tham khảo thêm :<https://github.com/css-modules/css-modules>

## 4. CSS Modules and Media Queries

Với media queries thì chúng ta sử dụng như bình thường thôi

```css
.font {
  font-family: "Open Sans";
}
.background {
  background-color: blue;
}
.person {
  padding: 20px;
  border: 1px solid #eeeeee;
  border-radius: 4px;
  margin-bottom: 30px;
  width: 60%;
  composes: font background;
}

.person:hover {
  box-shadow: 0 2px 4px #cccccc;
}
@media (max-width: 768px) {
  .background {
    background-color: red;
  }
  .person {
    width: 100%;
  }
}
```

Class font được kế thừa bởi person khi resize lại nó cũng đổi trong person. Cái này tùy các bạn sáng tạo thôi.

## 5. Inline Styles

Ngoài ra chúng ta có thể sử dụng inline style như sau:

```jsx
// App.js
// ...
const btnStyle = {
  backgroundColor: "#20232a",
  color: "#61dafb",
  padding: "8px 16px",
  border: "none",
  borderRadius: "4px",
  cursor: "pointer",
};
// ...
<button onClick={() => changePerson()} style={btnStyle}>
  Change person
</button>;
```

**Lưu ý** là style attribute nhận vào một Javascript object, các properties follow camelCased nha.

## 6. Setting Styles Dynamically

```jsx
const Person = (props) => {
  const clickHandle = () => {
    props.click();
  };
  const [fontSize, setFontSize] = useState("small");

  const handleChangeFont = () => {
    setFontSize((prev) => (prev === "small" ? "big" : "small"));
  };
  //   Can apply props
  const style = {
    backgroundColor: "red",
    fontSize: fontSize === "small" ? "10px" : "20px",
  };
  return (
    <div className={styles.person} style={style}>
      <p onClick={clickHandle}>I am {props.name}!</p>
      <p>{props.children}</p>
      <input type="text" onChange={props.change} value={props.name} />
      <button onClick={handleChangeFont}>Change font</button>
    </div>
  );
};

export default Person;
```

Cái này thì đơn giản thôi bạn có thể truyền props state quyết định là ở bạn

## 7. Additional: Using Radium for Pseudo and Media Queries

Với các cách đã nói trên bạn có thể style inline cho component được nhưng nếu bạn muốn cái style đó có các pseudo hoặc 1 thành phần chỉ định có media queries ứng riêng với nó.Nếu muốn như vậy thì bạn dùng Radium:
Đầu tiên cài Radium:

- Với js: **npm install --save radium**
- Với typescript: **npm install --save @types/radium**

Lưu ý:

- Với typescript : Anh em ra file App.js

```jsx
import React from "react";
import "./App.css";
import { Container } from "./components/Container";
import Radium from "radium";

function App() {
  return (
    <Radium.StyleRoot>
      <Container />
    </Radium.StyleRoot>
  );
}

export default App;
```

- Với javascript : Anh em ra file App.js

```jsx
import React from "react";
import "./App.css";
import { Container } from "./components/Container";
import { StyleRoot } from "radium";

function App() {
  return (
    <StyleRoot>
      <Container />
    </StyleRoot>
  );
}

export default App;
```

**Phải bọc vào anh em nhé không thì nó sẽ báo lỗi đấy: Uncaught Error: To use plugins requiring `addCSS`**

Tiếp theo là sử dụng nó nhé !!!

```jsx
import React, { FC, useState } from "react";
import "../css/person.css";
import Radium from "radium";

// Cái any bạn tự định nghĩa props truyền vào kiểu gì nhé với js thì k cần bỏ cái FC đi
const Person: FC<any> = (props) => {
  const [fontSize, setFontSize] = useState("small");

  const handleChangeFont = () => {
    setFontSize((prev) => (prev === "small" ? "big" : "small"));
  };
  //   Can apply props

  // Radium.StyleRules cho typescript js bỏ
  const style: Radium.StyleRules = {
    test: {
      width: "450px",
      backgroundColor: "red",
      "@media (min-width: 500px)": {
        backgroundColor: "blue",
      },
      ":hover": {
        backgroundColor: "black",
      },
    },
  };

  return (
    <div style={style.test}>
      <p>I am {props.name}!</p>
      <p>{props.children}</p>
      <input type="text" onChange={props.change} value={props.name} />
      <button onClick={handleChangeFont}>Change font</button>
    </div>
  );
};

export default Radium(Person);
```

**Lưu ý :**

- Anh em dùng cái media query inline của radium ý thì tất cả cái style trong đó sẽ có thêm !important.Anh em có thể bật devtool lên để xem nhé .
- Bọc cái đang dùng cái inline style bằng Radium(Person) như trên nếu không bọc như ví dụ trên nó sẽ chỉ nhận width và background chứ không nhận cái pseudo và media.

Link tham khảo thêm: <https://github.com/FormidableLabs/radium/tree/master/docs/api>

## 8. Additional: Using Styled Components and Dynamic Styles

Viết đến đây mệt vl cuối cùng là styled component anh em nhé!!!

Cài đặt:

- Với js : npm install styled-components
- Với typescript : npm install --save @types/styled-components

Bắt đầu nào

```jsx
import React, { FC, useState } from "react";
import styled from "styled-components";

const Name = styled.p`
  font-size: 1rem;
  font-family: "Helvetica Neue";
`;

const PersonName = styled(Name)`
  font-size: 1.5rem;
`;

const Input = styled.input`
  background: ${(props) => (props.type === "text" ? "blue" : "yellow")};
  border-radius: 5px;
`;

const Icon = styled.svg`
  flex: none;
  transition: fill 0.25s;
  width: 48px;
  height: 48px;
`;

const Link = styled.a`
  display: flex;
  align-items: center;
  padding: 5px 10px;
  background: papayawhip;
  color: palevioletred;

  &:hover ${Icon} {
    fill: red;
  }
`;

const Person = (props) => {
  return (
    <div>
      <Link href="#">
        <Icon viewBox="0 0 20 20">
          <path d="M10 15h8c1 0 2-1 2-2V3c0-1-1-2-2-2H2C1 1 0 2 0 3v10c0 1 1 2 2 2h4v4l4-4zM5 7h2v2H5V7zm4 0h2v2H9V7zm4 0h2v2h-2V7z" />
        </Icon>
      </Link>

      <PersonName>I am {props.name}!</PersonName>

      <Name>{props.children}</Name>

      <Input type="text" />
      <Input type="password" />
    </div>
  );
};

export default Person;
```

Ok ở đây mình giải thích nhé:

- Style component giúp chúng ta có thể extend các component đã được định nghĩa từ trước => Tiết kiệm code
- Viết pseudo class dễ dàng
- Dynamic style dựa trên props truyền vào

**Animation**

```jsx
const rotate = keyframes`
  from {
    transform: rotate(0deg);
  }

  to {
    transform: rotate(360deg);
  }
`;

const Rotate = styled.div`
  display: inline-block;
  animation: ${rotate} 2s linear infinite;
  padding: 2rem 1rem;
  font-size: 1.2rem;
`;

render(<Rotate>&lt; 💅🏾 &gt;</Rotate>);
```

Để định nghĩa các animations, Styled-Components cung cấp cơ chế sinh ra các unique name cho các keyframes ở mỗi components, bởi vậy chúng ta sẽ không cần lo lắng về việc tên của keyframes có bị trùng hay không nữa

**Lưu ý:**

- Styled component giúp chúng ta có thể extend từ những component có sẵn.
- Muốn dùng component có sẵn để dùng trong css của component này thì cần phải định nghĩa trước như trên thì nếu các bạn để Icon dưới Link thì nó sẽ đỏ lòm nhé.

Link tham khảo thêm :<https://styled-components.com>
