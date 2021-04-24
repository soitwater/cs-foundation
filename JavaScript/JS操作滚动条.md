# JS操作滚动条

## 代码
```js
<!DOCTYPE html>
<html lang="en">

<head>
  <style>
    * {
      padding: 0;
      margin: 0;
    }

    #box {
      width: 300px;
      height: 400px;
      border: 3px solid blue;
      margin: 20px auto;
      /*产生滚动条*/
      overflow: auto;
    }

    #text {
      text-align: center;
    }
  </style>
</head>

<body>
  <h1 id="text">0</h1>
  <button onclick='handleClick(`up`)'>up</button>
  <button onclick='handleClick(`down`)'>down</button>
  <div id="box">
    <p>
      ABCDEFG, HIJKLMN, OPQRST, UVWXYZ, 1234567890;ABCDEFG, HIJKLMN, OPQRST, UVWXYZ, 1234567890;ABCDEFG, HIJKLMN,
      OPQRST, UVWXYZ, 1234567890;ABCDEFG, HIJKLMN, OPQRST, UVWXYZ, 1234567890;
      ABCDEFG, HIJKLMN, OPQRST, UVWXYZ, 1234567890;ABCDEFG, HIJKLMN, OPQRST, UVWXYZ, 1234567890;ABCDEFG, HIJKLMN,
      OPQRST, UVWXYZ, 1234567890;ABCDEFG, HIJKLMN, OPQRST, UVWXYZ, 1234567890;
      ABCDEFG, HIJKLMN, OPQRST, UVWXYZ, 1234567890;ABCDEFG, HIJKLMN, OPQRST, UVWXYZ, 1234567890;ABCDEFG, HIJKLMN,
      OPQRST, UVWXYZ, 1234567890;ABCDEFG, HIJKLMN, OPQRST, UVWXYZ, 1234567890;
      ABCDEFG, HIJKLMN, OPQRST, UVWXYZ, 1234567890;ABCDEFG, HIJKLMN, OPQRST, UVWXYZ, 1234567890;ABCDEFG, HIJKLMN,
      OPQRST, UVWXYZ, 1234567890;ABCDEFG, HIJKLMN, OPQRST, UVWXYZ, 1234567890;
    </p>
  </div>
  <script>
    var box = document.querySelector('#box');
    function handleClick(type) {
      box.scrollTop = type === 'up' ? box.scrollTop - 5 : box.scrollTop + 5;
    }
  </script>
</body>

</html>
```