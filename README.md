# vue-book

> A Vue.js project

## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build

# build for production and view the bundle analyzer report
npm run build --report
```

For a detailed explanation on how things work, check out the [guide](http://vuejs-templates.github.io/webpack/) and [docs for vue-loader](http://vuejs.github.io/vue-loader).

## 一、vue-cli 搭建项目结构
#### 安装  npm install -g @vue/cli
#### 运行  vue init webpack vue-book

## 二、配置基础文件
#### 导入reset.css、border.css等文件
#### 搭建页面架构，配置相关路由

## 三、Tab组件开发
#### 伸缩布局
#### 阿里矢量图标的使用

## 四、Header组件开发
#### 使用插槽，复用组件
#### 

## 五、轮播图
#### vue-awesome-swiper插件 v2.6.7
##### 注意：插件的使用，需要使用vue.use(插件)
#### 使用nodejs搭建mock数据接口 
```
http.createServer((req,res) => {
    //设置响应头解决跨域
    .....
    //导入资源
    let sliders = require('./index.js')
    //获取请求url
    let {pathname,query} = url.parse(req.url);
    //获取轮播图
    if(pathname === '/sliders') {
        res.end(JSON.stringify(sliders))
    }
}).listen(4000)
```
#### 在api中使用axios发送ajax请求获取数据
#### 组件父传子渲染视图
##### 在swiper组件的父组件home中调用api获取数据，传递个子组件


### 六、开发热门图书组件(home-hot)
#### 模拟数据 books.json
#### node fs模块读取文件数据(readFile())
```
function read(callback) {
    fs.readFile('./books.json','utf8',(err,data) => {
        if(err) {
            callback([]);
        }
        callback(JSON.parse(data));
    });
}
```
#### axios设置拦截器，返回res.data
```
axios.interceptors.response.use(function (res) {
    return res.data;
},function (error) {
    return Promise.reject(error);
});
```

### 七、图书列表页开发
#### header组件
#### node写数据接口
#### 写api
#### 前端渲染
#### axios发送delete请求删除图书
```
export let removeBook = (id) => {
    //使用es6模板字符串
    return axios.delete(`/remove?id=${id}`);
}
```
#### 删除数据思路
##### 后端---传递id，过滤数组，重新写入文件
```
//读取文件原有资源
read(function(books) {
    //根据id过滤删除的数据
    books = books.filter(item => item.bookId !== query.id);
    //数据重新写入文件中
    write(books,function() {
        //删除成功后，遵循规范，向前端返回一个空对象（阮一峰 restful）
        res.end(JSON.stringify({}));
    });
});
```
##### 前端---过滤数组，或者重新发送请求请求数据()。。。。
```
//删除某本图书
async remove(id) {
    await removeBook(id);
    //前台处理删除
    this.allBooks = this.allBooks.filter(item => item.bookId !== id);
}
```

### 八、图书详情---在新的页面中展示书本信息，使用表单展示，可修改界面
#### 使用vue动态路由
```
//路由配置
{
    path: '/detail/:bid',
    name: 'detail',
    component: Detail
}
//
<router-link :to="{name:'detail',params:{bid:book.bookId}}"></router-link>
```
#### 路由参数会被设置到this.route.params,可以在组件中使用
```
async getData() {
    this.book = await getBook(this.$route.params.bid);
}
```
#### 处理事件冒泡
```
<!-- .stop处理事件冒泡 -->
<button class="remove" @click.stop="remove(book.bookId)">删除</button>
```

#### 修改图书信息 (PUT)
##### 后端接口 node.js
```
let str = '';
//获取前台发送的数据
req.on("data",chunck => {
    str += chunck;
});
req.on('end',() => {
    //将前端数据转化为json类型
    let book = JSON.parse(str);
    read(function(books) {
        //遍历books.json文件，如果找到与id匹配的项，
        //则用前台发来的数据替换，否则返回项本身
        books = books.map(item => {
            if (item.bookId === id) {
                return book;
            }
            return item;
        });
        //将新的数据写入books.json文件
        write(books,function() {
            res.end(JSON.stringify({}));
        });
    });
});
```
##### axios 发送put请求---将前端的数据发送到后台
```
export let updateBook = (id,data) => {
    return axios.put(`/update?id=${id}`,data);
}
```
##### 注意：发送put请求之前，浏览器会发送预请求options，请求通过才发送put请求
```
//设置响应头解决跨域问题
res.setHeader("Access-Control-Allow-Origin", "*");
res.setHeader("Access-Control-Allow-Headers", "X-Requested-With");
res.setHeader("Access-Control-Allow-Methods","PUT,POST,GET,DELETE,OPTIONS");
res.setHeader("Access-Control-Allow-Headers", "Content-Type,Access-Token");
res.setHeader("X-Powered-By",' 3.2.1')
res.setHeader("Content-Type", "application/json;charset=utf-8");
```


