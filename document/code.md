# 对象

> 数据一一复制到响应式对象中

```
function checkedType(e) {
  return Object.prototype.toString.call(e).slice(8, -1);
}
const replaceFormfield = (data, replaceData) => {
  // // let result;
  // if (checkedType(replaceData) === 'Object') {
  //   // data = {};
  // } else if (checkedType(replaceData) === 'Array') {
  //   // data = [];
  // } else {
  //   return data ?? replaceData;
  // }
  // console.log(data, replaceData);
  for (let i in replaceData) {
    console.log(i, replaceData[i], checkedType(replaceData[i]) === 'Object' || checkedType(replaceData[i]) === 'Array');
    if (checkedType(replaceData[i]) === 'Object' || checkedType(replaceData[i]) === 'Array') {
      console.log(data[i]);
      replaceFormfield(data[i], replaceData[i]);
    } else {
      console.log(data?.[i], replaceData?.[i]);
      replaceData[i] = data?.[i] ?? replaceData?.[i];
    }
  }
  // return data;
};
```

# URL

> url param转对象

```
function get_parse_link(link){
// new URL().searchParams 得到的是一个 URLSearchParams 对象
const urlObj = new URL(link);
const urlSearchParams = urlObj.searchParams;
// 配合 Object.fromEntries 将查询参数转换为对象
const paramObj = Object.fromEntries(urlSearchParams);
return paramObj
}
```

# cookie

> cookie

```
if (!navigator.cookieEnabled && window.location.href.includes('login')) {
console.log(window.location,window.parent.frames[0])
alert("您的浏览器限制了第三方Cookie，这将影响您正常登录，您可以更改浏览器的隐私设置，解除限制后重试。");
}
```



# 组件

## 导入

```

```



## 导出

```
const createFile = async res => {
  const { fileData, type, fileName } = res;
  // 创建一个blob链接
  // const blob1 = new Blob([data], { type: 'application/vnd.ms-excel' });
  const blob1 = new Blob([fileData]);
  console.log(blob1);
  const url = URL.createObjectURL(blob1);
  const a = document.createElement('a');
  a.setAttribute('download', url);
  a.href = url;
  a.style.display = 'none';
  a.setAttribute('download', fileName);
  document.body.appendChild(a);
  a.click();
  document.body.removeChild(a);
  // 每次调用URL.createObjectURL,都会创建一个新的URL对象，浏览器内存中会保持对该对象的引用
  // 只有在document销毁时，才会释放此部分内存
  // 在考虑性能的情况下，在url使用结束后，最好释放此部分内存
  URL.revokeObjectURL(url);
  return true;
};

/**
 * 下载文件
 * @param fileName
 * @param blob
 */
export function downLoadBlobFile(fileName: string, blob: any) {
  if ('download' in document.createElement('a')) {
    // 非IE下载
    const eLink = document.createElement('a');
    eLink.download = fileName;
    eLink.style.display = 'none';
    eLink.href = URL.createObjectURL(blob);
    document.body.appendChild(eLink);
    eLink.click();
    URL.revokeObjectURL(eLink.href);
    document.body.removeChild(eLink);
  } else if ((window as any).navigator) {
    // IE10+下载
    (window as any).navigator.msSaveBlob(blob, fileName);
  } else {
    Message.error('浏览器不支持下载');
  }
}
```

```
export function downloadExcel(blobParts: BlobPart, fileName = `${Date.now()}.xlsx`) {
  const blob = new Blob([blobParts], {
    type: 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet',
  });
  const elink = document.createElement('a');
  elink.download = fileName;
  elink.style.display = 'none';
  elink.href = window.URL.createObjectURL(blob);
  document.body.appendChild(elink);
  elink.click();
  window.URL.revokeObjectURL(elink.href);
  document.body.removeChild(elink);
}
```



## form表单

通过循环配置项实现表单渲染。通过递归实现表单属性值多层嵌套（对象或者数组）下任然能同步数据

![image-20240218163612847](code-img/image-20240218163612847.png)



- style

  - visible是否显示逻辑，配合联动

- model绑定表单值

  - 嵌套obj(a.b / a[0].b)，通过递归组件，从而递归表单值实现对表单值的绑定

  - 联动

    开始使用对象的配置项

    ```
    'attrDto.attrName': {
    label: '属性名称',
    type: 'input',
    methods: [
    {
    methodType: 'required',
    message: '请输入属性名称',
    trigger: 'blur'
    }
    ]
    },
    ```

    - 若有两个表单属性值一样，对象就会重复并合并
    - 对象属性值不一定就是表单值

- 规则

  - required

# js

## 代码合集收藏

https://mp.weixin.qq.com/s/w9iLd56H4xyXSlRwiKs04g

## 正则

### 空白符判断

```
  private isEmptyOrWhitespace(str) {
    return !str || /^\s*$/.test(str);
  }

```

## 占位

```
  //el-table表格中数据为空时，用符号 ‘--‘ 代替
  .cell:empty::after {
    content: '-';
    color: gray;
  }
```



## 时间

### 历时

```
const dateOaHandle = (e, index) => {
  let obj1 =
    index === 0
      ? processList.value[processList.value.length - 1].gmtStateChange
      : OAdata.value[OAdata.value.length - 1].gmtCreate;
  let obj2 = e.gmtCreate;
  var diff = obj2 - obj1;
  var dd = Math.floor(diff / (24 * 3600 * 1000));
  var rest1 = diff % (24 * 3600 * 1000);
  var dh = Math.floor(rest1 / (3600 * 1000));
  var rest2 = rest1 % (3600 * 1000);
  var dm = Math.floor(rest2 / (60 * 1000));
  var rest3 = rest2 % (60 * 1000);
  var ds = Math.floor(rest3 / 1000) + 1;
  return ' ' + '历时' + dd + '天' + dh + '小时' + dm + '分' + ds + '秒';
};
```

## JSON对象转换为 JSON 字符串添加换行符

> 最近做一个项目，项目中有一段代码编辑器，返回的代码段是JSON对象，需要解析成JSON字符串，然后，放到代码编辑器里面，但是JSON.stringify()默认返回的字符串没有缩进和换行，但是这个是可以配置的，记录一下。

JSON.stringify() 方法用于将 JavaScript 值转换为 JSON 字符串。

## 语法



```javascript
JSON.stringify(value[, replacer[, space]])
```

参数说明：

value:

必需， 要转换的 JavaScript 值（通常为对象或数组）。

replacer:

可选。用于转换结果的函数或数组。

如果 replacer 为函数，则 JSON.stringify 将调用该函数，并传入每个成员的键和值。使用返回值而不是原始值。如果此函数返回 undefined，则排除成员。根对象的键是一个空字符串：""。

如果 replacer 是一个数组，则仅转换该数组中具有键值的成员。成员的转换顺序与键在数组中的顺序一样。

space:

可选，文本添加缩进、空格和换行符，如果 space 是一个数字，则返回值文本在每个级别缩进指定数目的空格，如果 space 大于 10，则文本缩进 10 个空格。space 也可以使用非数字，如：\t。

案例

```javascript
JSON.stringify(data, null, 2);
```

[![img](https://img2020.cnblogs.com/blog/1334178/202010/1334178-20201010105216730-440382725.png)](https://img2020.cnblogs.com/blog/1334178/202010/1334178-20201010105216730-440382725.png)

完成！

# css

## 蒙版

```css
.page{
    position:relative;
    overflow: hidden;
}
.page:after{
    position:absolute;
    left: 0;
    top:0;
    display: block;
    width:100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    content: attr(data-text);
    /* transition:all 1s ease; */
    transform: translateY(-100%);
    color: #FFF;
}
/* .page:hover:after{
    transform: translateY(0);
} */
```

## 三角形

```css
#box {
    width: 0px;
    height: 0px;

    border: 100px solid transparent;

    border-top-color: deeppink;
    border-left-color: deeppink;
    /*border-right-color: deeppink;*/
    /*border-bottom-color: deeppink;*/
}
```



# 编程题

## 时间空间复杂度

log：https://blog.csdn.net/weixin_39888180/article/details/111268391

### 时间复杂度

其实就是一个函数，用大 O 表示， 比如 O(1)、 O(n)...

它的作用就是用来`定义描述算法的运行时间`

- **O(1)**

```
    let i = 0
    i += 1
复制代码
```

- **O(n)：** 如果是 O(1) + O(n) 则还是 O(n)

```
    for (let i = 0; i < n; i += 1) {
      console.log(i)
    }
复制代码
```

- **O(n^2)：** O(n) * O(n), 也就是双层循环，自此类推：O(n^3)...

```
    for (let i = 0; i < n; i += 1) {
      for (let j = 0; j < n; j += 1) {
        console.log(i, j)
      }
    }
复制代码
```

- **O(logn)：** 就是求 log 以 2 为底的多少次方等于 n

```
    // 这个例子就是求2的多少次方会大于i，然后就会结束循环。 这就是一个典型的 O(logn)
    let i = 1
    while (i < n) {
      console.log(i)
      i *= 2
    }
复制代码
```

###  空间复杂度

和时间复杂度一样，空间复杂度也是用大 O 表示，比如 O(1)、 O(n)...

它用来`定义描述算法运行过程中临时占用的存储空间大小`

> 占用越少 代码写的就越好

- **O(1)：** 单个变量，所以占用永远是 O(1)

```
    let i = 0
    i += 1
复制代码
```

- **O(n)：** 声明一个数组， 添加 n 个值， 相当于占用了 n 个空间单元

```
    const arr = []
    for (let i = 0; i < n; i += 1) {
      arr.push(i)
    }
复制代码
```

- **O(n^2)：** 类似一个矩阵的概念，就是二维数组的意思

```
    const arr = []
    for (let i = 0; i < n; i += 1) {
      arr.push([])
      for (let j = 0; j < n; j += 1) {
        arr[i].push(j)
      }
    }
```

### 查找

#### 二分法

二分法查找是一种速度非常快的算法，但是它有固定的应用范围。仅当列表是有序的时候，二分查找才管用。

  > 采用二分法，取出中间数，数组每次和中间数比较，小的放到左边，大的放到右边

### 排序

#### 快速排序

  - （1）在数据集之中，找一个基准点
  - （2）建立两个数组，分别存储左边和右边的数组
  - （3）利用递归进行下次比较

```js
var arr = [3, 1, 4, 6, 5, 7, 2];

function quickSort(arr) {
    if(arr.length == 0) {
        return [];    // 返回空数组
    }

    var cIndex = Math.floor(arr.length / 2);
    var c = arr.splice(cIndex, 1);
    var l = [];
    var r = [];

    for (var i = 0; i < arr.length; i++) {
        if(arr[i] < c) {
            l.push(arr[i]);
        } else {
            r.push(arr[i]);
        }
    }

    return quickSort(l).concat(c, quickSort(r));
}

console.log(quickSort(arr));
```

## 深拷贝与浅拷贝

https://mp.weixin.qq.com/s/BQ3_RJQcCZiXphMoyMRaTg

基本数据类型的特点：直接存储在栈(stack)中的数据
引用数据类型的特点：存储的是该对象在栈中引用地址，真实的数据存放在堆内存里。引用数据类型在栈中存储了指针，指针指向堆中该实体的起始地址

浅拷贝只复制指向某个对象的指针，而不复制对象本身，新旧对象还是共享同一块内存。但深拷贝会另外创造一个一模一样的对象，新对象跟原对象不共享内存，修改新对象不会改到原对象。

#### 实现浅拷贝

浅拷贝只拷贝基本数据的内容，引用数据虽然栈不同但会共用一个堆

##### Object.assign()

![图片描述](https://segmentfault.com/img/bVbrl7t?w=541&h=138)

注意：当object只有一层的时候，是深拷贝

##### 展开运算符...

```
let obj1 = { name: 'Kobe', address:{x:100,y:100}}
let obj2= {... obj1}
obj1.address.x = 200;
obj1.name = 'wade'
console.log('obj2',obj2) // obj2 { name: 'Kobe', address: { x: 200, y: 100 } }
```

##### concat()

```
let arr = [1, 3, {
    username: 'kobe'
    }];
let arr2 = arr.concat();    
arr2[2].username = 'wade';
console.log(arr); //[ 1, 3, { username: 'wade' } ]
```

##### slice()

![图片描述](https://segmentfault.com/img/bVbrmbB?w=424&h=185)

#### 实现深拷贝

![img](https://image-static.segmentfault.com/809/484/809484289-5cb44d922e659_fix732)

```js
function deepClone(obj, newObj) {
  var newObj = newObj || {};
  for (let key in obj) {
    if (typeof obj[key] == 'object') {
      let isArray = Array.isArray(obj[key]);//判断是否数组
      newObj[key] = (isArray == true ? [] : {})
      deepClone(obj[key], newObj[key]);
    } else {
      newObj[key] = obj[key]
    }
  }
  console.log(newObj);
  return newObj;
}

let a1 = { a: { b: 1 } }
let a2 = deepClone(a1);
a1.a.b = 2;
console.log(a2.a);
```




```js
function deepClone(source) {
    let target;
    if (typeof source === 'object') {
      target = Array.isArray(source) ? [] : {}
      for (let key in source) {
        if (source.hasOwnProperty(key)) {
          if (typeof source[key] !== 'object') {
            target[key] = source[key]
          } else {
            target[key] = deepClone(source[key])
          }
        }
      }
    } else {
      target = source
    }
    return target
  }
  let a ={name:['a','b'],age:[10,15],book:{1:[2,3,k]}}
  let b = deepClone(a)
  console.log(a)
  console.log(a == b)
  let c = a
  console.log(c)
  console.log(c == a)
```

```js
JSON.parse(JSON.stringify(xxx))

我们在使⽤ JSON.parse(JSON.stringify(xxx))时应该注意⼀下⼏点：
1、如果obj⾥⾯存在时间对象，JSON.parse(JSON.stringify(obj))之后，时间对象变成了字符串。
2、如果obj⾥有RegExp、Error对象，则序列化的结果将只得到空对象。
3、如果obj⾥有函数，undefined，则序列化的结果会把函数， undefined丢失。
4、如果obj⾥有NaN、Infinity和-Infinity，则序列化的结果会变成null。
5、JSON.stringify()只能序列化对象的可枚举的⾃有属性。如果obj中的对象是有构造函数⽣成的，则使⽤JSON.parse(JSON.stringify(obj))
深拷贝后，会丢弃对象的constructor。
6、如果对象中存在循环引⽤的情况也⽆法正确实现深拷贝。
```

## 递归

### 阶乘

```js
  const factorial = function(n) {
      if (n <= 1) {
          return 1;
      }
      return n * factorial(n - 1);
  }
```

f(6) = n * f(5)，所以 f(6) 需要拆解成 f(5) 子问题进行求解，以此类推 f(5) = n * f(4) ，也需要进一步拆分 ... 直到 f(1)，「这是递的过程。」 f(1) 解决后，依次可以解决f(2).... f(n)最后也被解决，「这是归的过程。」

归无非就是把问题拆解成具有相同解决思路的子问题，直到最后被拆解的子问题不能够拆分，这个过程是“递”。当解决了最小粒度可求解的子问题后，在“归”的过程中顺其自然的解决了最开始的问题。

  **复杂度分析**

  - 空间复杂度为 O(n)
  - 时间复杂度 O(2^n)

```
  总时间 = 子问题个数 * 解决一个子问题需要的时间
```

  - 子问题个数即递归树中的节点总数 2^n
  - 解决一个子问题需要的时间，因为只有一个加法操作 `fib(n-1) + fib(n-2)` ，所以解决一个子问题的时间为 `O(1)`

  二者相乘，得出算法的时间复杂度为 `O(2^n)`

### 实现深拷贝

![img](https://image-static.segmentfault.com/809/484/809484289-5cb44d922e659_fix732)

```js
function deepClone(obj, newObj) {
  var newObj = newObj || {};
  for (let key in obj) {
    if (typeof obj[key] == 'object') {
      let isArray = Array.isArray(obj[key]);//判断是否数组
      newObj[key] = (isArray == true ? [] : {})
      deepClone(obj[key], newObj[key]);
    } else {
      newObj[key] = obj[key]
    }
  }
  console.log(newObj);
  return newObj;
}

let a1 = { a: { b: 1 } }
let a2 = deepClone(a1);
a1.a.b = 2;
console.log(a2.a);
```




```js
function deepClone(source) {
    let target;
    if (typeof source === 'object') {
      target = Array.isArray(source) ? [] : {}
      for (let key in source) {
        if (source.hasOwnProperty(key)) {
          if (typeof source[key] !== 'object') {
            target[key] = source[key]
          } else {
            target[key] = deepClone(source[key])
          }
        }
      }
    } else {
      target = source
    }
    return target
  }
  let a ={name:['a','b'],age:[10,15],book:{1:[2,3,k]}}
  let b = deepClone(a)
  console.log(a)
  console.log(a == b)
  let c = a
  console.log(c)
  console.log(c == a)
```

```js
JSON.parse(JSON.stringify(xxx))

我们在使⽤ JSON.parse(JSON.stringify(xxx))时应该注意⼀下⼏点：
1、如果obj⾥⾯存在时间对象，JSON.parse(JSON.stringify(obj))之后，时间对象变成了字符串。
2、如果obj⾥有RegExp、Error对象，则序列化的结果将只得到空对象。
3、如果obj⾥有函数，undefined，则序列化的结果会把函数， undefined丢失。
4、如果obj⾥有NaN、Infinity和-Infinity，则序列化的结果会变成null。
5、JSON.stringify()只能序列化对象的可枚举的⾃有属性。如果obj中的对象是有构造函数⽣成的，则使⽤JSON.parse(JSON.stringify(obj))
深拷贝后，会丢弃对象的constructor。
6、如果对象中存在循环引⽤的情况也⽆法正确实现深拷贝。
```

### 树形结构

```js
const input = [
  {
    name: "浙江",
    children: [
      {
        name: "杭州",
        children: [
          { name: "余杭区" },
          { name: "上城区" },
          { name: "下城区" },
          { name: "西湖区" },
          { name: "拱墅区" },
        ],
      },
      {
        name: "绍兴",
        children: [
          { name: "柯桥区" },
          { name: "越城区" },
          { name: "上虞区" },
          { name: "新昌县" },
        ],
      },
    ],
  },
  {
    name: "湖北",
    children: [
      {
        name: "武汉",
        children: [
          { name: "江岸区" },
          { name: "江汉区" },
          { name: "硚口区" },
          { name: "汉阳区" },
          { name: "武昌区" },
          { name: "青山区" },
          { name: "洪山区" },
        ],
      },
    ],
  },
];
```

#### 获取树的叶子节点并赋值到树结构中

```js
const input = [
  {
    name: "浙江",
    children: [
      {
        name: "杭州",
        children: [
          { name: "余杭区" },
          { name: "上城区" },
          { name: "下城区" },
          { name: "西湖区" },
          { name: "拱墅区" },
        ],
      },
      {
        name: "绍兴",
        children: [
          { name: "柯桥区" },
          { name: "越城区" },
          { name: "上虞区" },
          { name: "新昌县" },
        ],
      },
    ],
  },
  {
    name: "湖北",
    children: [
      {
        name: "武汉",
        children: [
          { name: "江岸区" },
          { name: "江汉区" },
          { name: "硚口区" },
          { name: "汉阳区" },
          { name: "武昌区" },
          { name: "青山区" },
          { name: "洪山区" },
        ],
      },
    ],
  },
];
function process(input) {
  function getLeafCountTree(arr) {
    if (!arr.children) {
      return 1;
    } else {
      arr.leafCount = 0;
      for (var i = 0; i < arr.children.length; i++) {
        arr.leafCount = arr.leafCount + getLeafCountTree(arr.children[i]);
      }
      //需要将次层的叶子节点return给父级
      return arr.leafCount;
    }
  }
  function finalQuery() {
    //因为是数组，不是树形结构，需要以树形结构处理
    for (var r = 0; r < input.length; r++) {
      getLeafCountTree(input[r]);
    }
  }
  finalQuery();
  return input
}
console.log(process(input));

const output = [
  {
    name: "浙江",
    path: "浙江",
    leafCount: 9,
    children: [
      {
        name: "杭州",
        path: "浙江-杭州",
        leafCount: 5,
        children: [
          { name: "余杭区", path: "浙江-杭州-余杭区" },
          { name: "上城区", path: "浙江-杭州-上城区" },
          { name: "下城区", path: "浙江-杭州-下城区" },
          { name: "西湖区", path: "浙江-杭州-西湖区" },
          { name: "拱墅区", path: "浙江-杭州-拱墅区" },
        ],
      },
      {
        name: "绍兴",
        path: "浙江-绍兴",
        leafCount: 4,
        children: [
          { name: "柯桥区", path: "浙江-绍兴-柯桥区" },
          { name: "越城区", path: "浙江-绍兴-越城区" },
          { name: "上虞区", path: "浙江-绍兴-上虞区" },
          { name: "新昌县", path: "浙江-绍兴-新昌县" },
        ],
      },
    ],
  },
  {
    name: "湖北",
    path: "湖北",
    leafCount: 7,
    children: [
      {
        name: "武汉",
        path: "湖北-武汉",
        leafCount: 7,
        children: [
          { name: "江岸区", path: "湖北-武汉-江岸区" },
          { name: "江汉区", path: "湖北-武汉-江汉区" },
          { name: "硚口区", path: "湖北-武汉-硚口区" },
          { name: "汉阳区", path: "湖北-武汉-汉阳区" },
          { name: "武昌区", path: "湖北-武汉-武昌区" },
          { name: "青山区", path: "湖北-武汉-青山区" },
          { name: "洪山区", path: "湖北-武汉-洪山区" },
        ],
      },
    ],
  },
];
```

#### 根据id，拿到id数组

```js
// (tree为目标树，targetId为目标节点id)
function getNodeRoute(tree, targetId) {
  let nodePathArray = []
  let fn = (tree, targetId) => {
    for (let index = 0; index < tree.length; index++) {
      if (tree[index].children) {
        let endRecursiveLoop = fn(tree[index].children, targetId)
        if (endRecursiveLoop) {
          nodePathArray.push(tree[index].name)
          return true
        }
      }
      if (tree[index].name === targetId) {
        nodePathArray.push(tree[index].name)
        return true
      }
    }
  }
  fn(tree, targetId)
  return nodePathArray;
}

getNodeRoute(input, '余杭区')
console.log(getNodeRoute(input, '余杭区').reverse());
//[ '浙江', '杭州', '余杭区' ]
```

####   案例

- 使用递归实现`getElementsByClassName`

  ```js
  let arr = [];
     function byClass(node, className, arr){
         //得到传入节点的所有子节点
         var lists = node.childNodes;
         for(var i = 0;i< lists.length;i++){
             //判断是否有相同className元素
             if(arr[i],className == className){
                 arr.push(arr[i]);
             }
             //判断子节点是否还有子节点
             if(arr[i].childNodes.length > 0){
                 byClass(arr[i],className,arr);
             }
         }
     }
  复制代码
  ```

- 有一堆桃子，每天吃掉一半，挑出一个坏的扔掉，第6天的时候发现还剩1个桃子，问原来有多少个桃子。


```js
  function fn(n) {
    if (n === 1) {
      return 1
    }
    var a = (fn(n - 1) + 1) * 2
    return a
  }
  console.log(fn(2)) //190
```



# 浏览器

将浏览器的前进按钮禁止

```js
// 将浏览器的前进按钮禁止
import $ from "jquery";
$(function () {
  if (window.history && window.history.pushState) {
    $(window).on('popstate', function () {
      // console.log(window.location.href)
      if (window.location.href.indexOf(window.location.origin + "/wechatpub/surveyOne")>-1) {
        // console.log(window.location.href,"==========================")
        window.history.pushState('forward', null, '#');
        window.history.forward(1);
      }
    });
  }
  // window.history.pushState('forward', null, '#'); //在IE中必须得有这两行
  // window.history.forward(1);
})
```

## 获取url params

https://www.cnblogs.com/linzhifen5/p/16960959.html

在JavaScript中，获取URL参数可以通过`URLSearchParams`对象实现。以下是一个简单的函数，用于获取指定URL参数的值：

```javascript
function getUrlParam(paramName) {
  const urlParams = new URLSearchParams(window.location.search);
  return urlParams.get(paramName);
}
 
// 使用示例
const paramValue = getUrlParam('paramName'); // 假设URL是 'http://example.com/?paramName=value'
console.log(paramValue); // 输出：value
```

在这个例子中，`getUrlParam`函数接受一个参数名`paramName`，然后创建了一个`URLSearchParams`实例来解析当前页面URL的查询字符串。`urlParams.get(paramName)`用于获取指定的URL参数值。如果URL中不存在该参数，则返回`null`。



## js 删除url 某个params

```js
function removeParam(url, key) {
    // 使用正则表达式匹配参数
    const pattern = new RegExp('(\\?|&)' + key + '=[^&]+');
    // 移除匹配到的参数
    return url.replace(pattern, '');
}
 
// 示例使用
const originalUrl = 'http://example.com/?key1=value1&key2=value2&key3=value3';
const newUrl = removeParam(originalUrl, 'key2');
 
console.log(newUrl); // 输出: http://example.com/?key1=value1&key3=value3
```



# 组件

## 空页面

```vue
<template>
  <div style="height: 100%; width: 100%" class="wrap">
    <div style="display: flex; justify-content: center" v-if="props.loading">
      <img class="rotate" src="@/assets/icon-svg/loading.svg" alt="" />
    </div>
    <div class="wrap" v-else>
      <img
        style="width: 50%; object-fit: contain; margin-left: 10px"
        src="@/assets/img/data_empty.png"
        alt=""
      />
      <div class="no-data">{{ props.title ? props.title : '暂无数据' }}</div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, PropType } from 'vue';
const props = defineProps({
  title: String,
  loading: Boolean
});
console.log(props);
</script>

<style scoped>
.data-empty {
  min-width: none;
}
.wrap {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  .no-data {
    font-size: 14px;
    font-family: PingFangSC-Regular, PingFang SC;
    font-weight: 400;
    color: rgba(34, 34, 34, 0.55);
    line-height: 14px;
    margin-top: 20px;
  }
}
.rotate {
  animation: rotate-ani 2s linear infinite;
  width: 50%;
  object-fit: contain;
}
@keyframes rotate-ani {
  from {
    transform: rotate(0);
  }
  to {
    transform: rotate(360deg);
  }
}
</style>

```



# ele

## tree

### node

```
{
  canFocus: false,
  checked: true,
  childNodes: [],
  data: {},
  expanded: false,
  id: 6172,
  indeterminate: false,
  isCurrent: false,
  isLeaf: true,
  level: 4,
  loaded: false,
  loading: false,
  parent: {},
  store: {},
  text: null,
  visible: undefined,
};

checked(选中)、unchecked(未选中)、indeterminate(部分选中)
```



### 获取筛选后的node

通过visible判断

```

const callback = (data, list) => {
  list &&
    list.forEach(val => {
      data.push({ id: val.data.id, visible: val.visible });
      if (val.childNodes.length) {
        callback(data, val.childNodes);
      }
    });
};
const handleCheckAllChange = val => {
  const ids = [];
  const dataList = [];
  callback(dataList, [treeRef.value.getNode(addProvePage.treeData[0].id)]);
  if (val) {
    // ruleForm.permissionList = [{}];
    console.log(dataList, ids);
    dataList.forEach((i, n) => {
      if (i.visible) {
        treeRef.value.setChecked(i.id, true);
        addProvePage.checkedKeys[addProvePage.tabActive] = [
          ...addProvePage.checkedKeys[addProvePage.tabActive],
          i.id
        ];
      }
    });
  } else {
    dataList.forEach((i, n) => {
      if (i.visible) {
        treeRef.value.setChecked(i.id, false);
      }
    });
  }
  addProvePage.isIndeterminate = false;
  checkCommon();
};
```

### css控制选择框展示

```
 <el-tree-select
 node-key="id"
 v-model="containForm.roleCopy"
 :data="roleOption"
 :props="optionProps"
 ref="selectRef"
 multiple
 show-checkbox
 popper-class="aaa"
 :check-on-click-node="false"
 >
</el-tree-select>

<style lang="scss">
.aaa {
  .el-checkbox {
    display: none;
  }
  .el-tree {
    .el-tree-node__content:has(i.is-leaf) {
      .el-checkbox {
        display: flex;
      }
    }
  }
}
</style>
```

## upload

### 上传图片

```js
            <el-upload
              class="avatar-uploader"
              method="post"
              ref="uploadRef"
              :action="getUrl"
              :show-file-list="false"
              :on-success="handleAvatarSuccess"
              :on-progress="handleAvatarProgress"
              :before-upload="beforeAvatarUpload"
              :on-change="handleChange"
            >
              <div class="upload">
                <!-- 成功 -->
                <div
                  v-if="imgStatus === 'success'"
                  :class="ishover ? 'center mask' : 'center'"
                  @mouseenter="ishover = true"
                  @mouseleave="ishover = false"
                >
                  <img
                    :src="imageUrl"
                    class="avatar"
                    style="object-fit: contain"
                  />
                  <div class="avatar-uploader-icon" v-if="ishover">
                    <el-icon
                      style="margin-right: 16px; color: #eee"
                      @click.stop="dialogImgVisible = true"
                      ><View
                    /></el-icon>
                    <el-icon style="color: #eee" @click.stop="deleteImg"
                      ><Delete
                    /></el-icon>
                  </div>
                </div>
                <!-- 失败 -->
                <div v-else-if="imgStatus === 'error'" class="center">
                  <img
                    :src="imageUrl"
                    class="avatar"
                    style="object-fit: scale-down"
                  />
                  <div class="avatar-uploader-icon">
                    <el-icon
                      style="
                        margin-right: 16px;
                        color: #eee;
                        background: red;
                        border-radius: 20px;
                      "
                      @click.stop="reload"
                      ><RefreshRight
                    /></el-icon>
                    <el-icon style="color: #eee" @click.stop="deleteImg"
                      ><Delete
                    /></el-icon>
                  </div>
                </div>
                <!-- 上传 -->
                <div v-else-if="imgStatus === 'upload'" class="center">
                  <el-progress
                    type="circle"
                    class="avatar-uploader-icon"
                    :percentage="percentage"
                  />
                  <img
                    v-if="imageUrl"
                    :src="imageUrl"
                    class="avatar"
                    style="object-fit: scale-down"
                  />
                </div>
                <!-- 初始化 -->
                <div v-else style="flex-direction: column; align-items: center">
                  <el-icon
                    class="avatar-uploader-icon"
                    style="color: rgba(34, 34, 34, 0.55)"
                    ><Plus
                  /></el-icon>
                  <span style="height: 16px"> 上传图片</span>
                </div>
              </div>
            </el-upload>

let fileArr: any = ref([]);
const ishover = ref(false);
const getUrl = computed(() => {
  // return 'http://localhost:8080/authpend/api/pending/todoSystemConfig/uploadFile';
  return '/auth-pend/api/pending/todoSystemConfig/uploadFile';
});
const reload = () => {
  console.log(fileArr);
  uploadRef.value.clearFiles();
  percentage.value = 0;
  imgStatus.value = 'init';
  // 通过on-success 方法获取到上传的文件
  uploadRef.value.handleStart(fileArr.value[0].raw);
  // 提交上传
  uploadRef.value.submit();
};
const deleteImg = () => {
  uploadRef.value.clearFiles();
  imgStatus.value = 'init';
  imageUrl.value = '';
  dialogState.realUrl = '';
};
const handleAvatarSuccess: UploadProps['onSuccess'] = (
  response,
  uploadFile
) => {
  imageUrl.value = URL.createObjectURL(uploadFile.raw!);
  dialogState.realUrl = response.data;
};
const handleAvatarProgress = e => {
  console.log(e);
  percentage.value = e.percent;
  if (e.isTrusted) {
    imgStatus.value = 'success';
  } else {
    imgStatus.value = 'error';
  }
};
const handleChange = (file, fileList) => {
  fileArr.value = [file];
  console.log(fileArr);
  // fileArr.value = [file];
  imageUrl.value = URL.createObjectURL(file.raw);
};
const beforeAvatarUpload: UploadProps['beforeUpload'] = rawFile => {
  if (!dialogState.imgTypeList.includes(rawFile.type)) {
    ElMessage.error('请传入对应格式的图片!');
    return false;
  } else if (rawFile.size / 1024 / 1024 > 5) {
    ElMessage.error('大小超过5MB!');
    return false;
  }
  imgStatus.value = 'upload';
  return true;
};
```



### 上传文件

```vue
<template>
  <div>
    <el-button style=" margin-left: 10px" type="success" @click="uploadVisible = true">导入Excel</el-button>
    <el-dialog :title="`导入${exportConfig.fileType}`" :model-value="uploadVisible" width="540px" size="540px" @close="cancel" :close-on-click-modal="false">
      <div class="upload">
        <div style="flex-direction: column">
          <span class="m-b-20"><span style="color: red">*</span>上传文件</span>
          <el-upload
            v-if="a == 1"
            ref="upload"
            :show-file-list="false"
            action=""
            :file-list="fileList"
            :on-change="handleChange"
            :auto-upload="false"
            accept=".xlsx"
            drag
            multiple
            :limit="1"
            :on-exceed="handleExceed"
            :disabled="props.status === 'loading'"
          >
            <template #trigger>
              <div>
                <svg class="icon" aria-hidden="true">
                  <use href="#icon-tamesicon-file" />
                </svg>
                <!-- <div class="el-upload__tip text-red">limit 1 file, new file will cover the old file</div> -->
                <div class="el-upload__text m-t-20">点击或将文件拖拽到这里上传</div>
                <div class="el-upload__tip" slot="tip">只允许上传单个Excel文件</div>
                <el-button type="primary" link @click.stop="download">下载模板</el-button>
              </div>
            </template>
            <template #tip> </template>
          </el-upload>
          <div v-if="a === 2" class="flex-column flex-align-center content">
            <!-- {{fileArr}} -->
            <img class="prefix-icon m-b-20" style="width: 48px" src="@/assets/icons/excel3.svg" alt="" />
            <span> {{ fileArr[0].name }}</span>
            <div class="flex-justify-end m-t-20">
              <el-button @click.stop="restart">重新上传</el-button>
              <el-button type="primary" @click.stop="handleBtn(2)">点击上传</el-button>
            </div>
          </div>
          <div v-if="props.status === 'loading'" class="flex-column flex-align-center content">
            <el-button style="border: 0" link :loading="loading"></el-button>
          </div>
          <div v-if="a === 3" class="flex-column flex-align-center content">
            <el-icon class="icon" style="margin-right: 5px; color: #52c41a; font-size: 84px">
              <i-custom-success />
            </el-icon>
            <div class="m-t-20">导入成功</div>
          </div>
          <div v-if="a === 4" class="flex-column flex-align-center content">
            <el-icon class="icon" style="margin-right: 5px; color: #f33; font-size: 84px">
              <i-custom-delete />
            </el-icon>
            <div class="m-t-20">导入失败</div>
            <el-button type="primary" class="m-b-12" @click.stop="restart">重新上传</el-button>
            <slot></slot>
          </div>
        </div>
      </div>
    </el-dialog>
  </div>
</template>

<script lang="ts" setup>
import type { UploadInstance, UploadProps, UploadRawFile } from 'element-plus';
const props = defineProps<{
  type: string;
  exportConfig: any;
  status: any;
}>();
const emit = defineEmits(['exportHandle']);
const { status } = toRefs(props);
const a = ref(1);
const { exportConfig } = toRefs(props);
let fileList = ref([]);
const uploadVisible = ref();
const upload = ref<UploadInstance>();
const cancel = () => {
  uploadVisible.value = false;
  a.value = 1;
  fileArr.value = [];
};
const download = () => {
  emit('exportHandle', { type: 'download' });
};
const loading = computed(() => {
  return props.status === 'loading' ? true : false;
});

watch(
  () => props.status,
  () => {
    console.log(123, props.status);
    if (props.status === 'success') {
      a.value = 3;
    } else if (props.status === 'error') {
      a.value = 4;
    } else if (props.status === 'loading') {
      a.value = 5;
    }
  }
);
const restart = () => {
  fileArr.value = [];
  a.value = 1;
  // upload.value!.clearFiles();
};

const handleExceed: UploadProps['onExceed'] = (files) => {
  upload.value!.clearFiles();
  // console.log();
  // const file = files[0] as UploadRawFile;
  // file.uid = genFileId();
  // upload.value!.handleStart(file);
};

let fileArr: any = ref([]);
const handleBtn = (e) => {
  let data = new FormData();
  console.log(fileArr.value);
  console.log(data, fileArr.value[0], fileList.value);
  data.append('file', fileArr.value[0].raw);
  // console.log(fileList.value, fileArr);
  // console.log(data);
  // if (fileList.length > 0) {
  //   this.fileList = [fileList[fileList.length - 1]]; // 这一步，是 展示最后一次选择的csv文件
  // }
  emit('exportHandle', { data, type: 'export' });
};
const handleChange = (file, fileList) => {
  console.log(132, file);
  fileList.value = fileList;
  fileArr.value = [file];
  // status.value = 'file';
  a.value = 2;
  console.log(fileArr.value, fileList, fileList.value);
};
// const fileHandle = () => {
//   emit('upload', data);
// };
defineExpose({ cancel });
</script>
<style lang="scss" scoped>
// :deep(.el-upload, ) {
//   height: 100%;
//   width: 100%;
//   background: #fafafa;
//   border-radius: 4px;
//   border: 1px solid #a1a1a1;

//   .el-upload-dragger {
//     background: #fafafa;
//   }
// }
// .upload {
//   width: 424px;
//   height: 256px;
// }
.el-upload__tip {
  color: rgba(34, 34, 34, 0.55);
}

.icon {
  width: 100px !important;
  height: 80px !important;
}

.content {
  height: 260px;
  justify-content: center;
  border: 1px #ccc;
  border-style: dashed;
}
</style>

```

## date-picker

### defaultTime

```
DatePickerProps.defaultTime = { type: Boolean, default: [new Date(0, 0, 0, 0, 0, 0), new Date(0, 0, 0, 23, 59, 59)] };

```



## menu

```
    <el-menu :router="true" popper-effect="dark" @select="selectMenu" default-active="2" class="el-menu-vertical-demo" :collapse="isCollapse">
      <div v-for="(val1, index1) in menuData" :key="index1">
        <el-menu-item v-if="!val1.zones" :index="`${val1.url}`">
          <svg class="icon" aria-hidden="true" v-if="val1.type === 'overview'">
            <use href="#icon-icon_overview" />
          </svg>
          <svg class="icon" aria-hidden="true" v-if="val1.type === 'dataUse'">
            <use href="#icon-icon_datause" />
          </svg>
          <template #title>
            {{ val1.name }}
          </template>
        </el-menu-item>
        <el-sub-menu v-else :index="`${index1}`" popper-class="sub">
          <template #title>
            <i-custom-data-operations v-if="val1.type === 'dataModal'" />
            <img v-if="val1.type === 'script'" style="width:16px;margin-left:32px" src="@/assets/script/script-color.svg"></img>
          </template>
          <el-menu-item-group style="width: 160px;">
            <el-menu-item :style="{background:menuIndex===val2.url?'#224BB3':''}" v-for="(val2, index2) in val1.zones" :key="index2" :index="`${val2.url}`">
              {{ val2.name }}
            </el-menu-item>
          </el-menu-item-group>
        </el-sub-menu>
      </div>
    </el-menu>
    
    
.menu-tree {
  width: 58px;
  .fold-up {
    padding: 10px;
    display: flex;
    flex-direction: row;
    justify-content: center;
    transform: rotate(180deg);
  }

  :deep(.el-tree-node__content) {
    border: 10px;
    display: flex;
    flex-direction: row;
    justify-content: center;

    .icon {
      margin: 0;
    }

    .title {
      display: none;
    }
  }
}

:deep(.el-tree-node__expand-icon.is-leaf) {
  display: none;
}

:deep(.el-tree-node__content > .el-tree-node__expand-icon) {
  display: none;
}

:deep(.el-menu--collapse) {
  width: initial;
}

:deep(.el-menu) {
  width: initial;
  background: #20243b;

  .el-sub-menu__title{
    padding: 0;

    &:hover{
      background: #2D324B;   
    }

    svg {
      margin-left: 27px;
    }
  }

  .el-menu-item{
    &:hover{
      background: #2D324B;   
    }
  }

  .el-menu-item.is-active{
    background: #224BB3;
  }

  .el-sub-menu.is-active{
    background: #224BB3;
  }

  .el-menu-item .el-menu-tooltip__trigger{
    width: initial;
  }

  .el-sub-menu .el-sub-menu__icon-arrow {
    right: 5px;
    display: none;
  }
}

.sub{  
  // width: 160px;  

  // :deep(.el-menu--popup) {
  // padding:0 !important;
  //   margin:0;  width: 160px;  
  // }

  .el-menu-item{ 
     color: #fff;
    &:hover{
      background: #2D324B;   
    }
  }


  .el-menu-item-group{  
    color: #fff;
    background: #20243B;
    padding:0;
    margin:0;
    border-radius: 10px;
  }
}

</style>

<style>
   .el-menu--popup{
    padding:0 !important;
    margin:0;
    min-width: 160px !important;
    border-radius: 10px;
  }
</style>
```










# AntD

### Form

```
rules={[
    ({ getFieldValue }) => ({
        validator(_, value) {
        if (value.valueOf() > getFieldValue('onlineTime').valueOf() 					|| !value) {
        	return Promise.resolve();
        }
        return Promise.reject(new Error('上线时间大于当前时间'));
        },
    }),
]}
```

  

```
const formRef = useRef<any>();
formRef.current.setFieldsValue({
    putNum: state.crowdData.putNum,
    putPhone: state.crowdData.putPhone,
    putEmail: state.crowdData.putEmail
})
<Form ref={formRef} initialValues={initialValues} {...Formlayout}><Form>
```

### upload

```jsx
  const uploadProps = {
    // name: "file",
    action: "",
    accept: ".doc, .docx",
    multiple: false, //是否支持多选文件
    fileList: uploadTypeList,
    showUploadList: {
      showDownloadIcon: true,
      downloadIcon: 'download ',
      showRemoveIcon: true,
      removeIcon: <i className='iconfont iconshanchu3' onClick={() => removeIcon()}/>,
    },
    iconRender() {
      return <img src={`${PUBLIC_CDN_URL}/images/dashboard/upload_file.png`} alt='' 			className={styles.upload_file}/>
    },
    onRemove() {
      setUploadTypeList([])
    }
    onChange(info) {
      console.log(info, uploadTypeList)
      let ext = info.file.name ? info.file.name.split('.')[1] : '';
      let reg = /[*&<>%?:;\\/|"]/im
      if (ext) {
        if (!(ext === 'doc' || ext === 'docx')) {
          message.error('您只能上传doc，docx 文件!');
        } else if (reg.test(info.file.name)) {
          message.error('文件名不能包含下列任何字符串：* & < > % ? : ; \\ / | "');
          return false;
        } else {
          message.loading({ content: '文件上传中...', key: 'export' });
          const formData = new FormData();
          formData.append('file_upload', info.file);
          console.log(info, uploadTypeList)
          // setUploadLocalList([info.file])
          // uploadTopic.run(formData);
        }
      } else {
      }
    },
  }
```
