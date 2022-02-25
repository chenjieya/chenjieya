# element
1. 邮箱校验问题：
```html
<input type='maile'>
```  

**原因：**在校验的时候会将句号（。）转换成英文的句号（.），所以在框中输入中文的句号也会通过校验。

**解决方法：**
将input框换成普通的文本框，就不会进行转义。
校验的方法，就是判断句号（。）是否存在。 （indexOf）

2. 表格：动态表头的时候，可能会出现横向滚动条和纵向的滚动条。此时会出现列的错位现象（百度了很多方法，不能解决我的问题）

**原因：**和官网的表格进行了结构对比，我的结构中少了class为gutter的th。

**解决方法：**
在渲染完表格之后，动态的添加了th。
```js
this.$nextTrick(()=>{
    let tr = document.getElementsByTagName('tr')[0];
    let th = document.createElement('th');
    th.className = 'gutter';
    tr.appendChild(th);
})
```

3. 表格：固定表格列的时候，表格的行会出现错位的现象。

**原因：**经过查找发现，element渲染固定列表格的时候，是通过生成的类似的div表格，覆盖到了以前的表格列上（在生成div表格的时候，计算高度出现了问题）

**解决方法：**

获取到表格内容部分的高度，然后计算固定列的div的高度，进行重新赋值。
```js
let tableWrapper = document.getElementsByClassName('el-table__body-wrapper')[0];  //获取到表格内容高度的元素
let fixMisplace = document.getElementsByClassName('el-table__fixed-body-wrapper')[0];  //获取到固定列的div的元素
setTimeout(()=>{  //需要加一个延时，有可能表格还没有渲染完毕，执行代码的时候，获取的高度不准确
    let tableHeight = tableWrapper.style.height.replace(/[p][x]/g, '');  //表格内容的高度
    let newHeight = +tableHeight - 18;   //减去滚动条的高度
    fixMisplace.style.height = newHeight + 'px';
},10)
```

4. 表格勾选：表格可以勾选，但是只能勾选五条，当勾选第六个的时候进行提示不能进行勾选。

**出现的问题：**当时经过一系列的操作，出现的情况是：勾选第六条的时候，会出现弹框，但是同时第六条数据也被勾选上了（没有达到想要的效果）

**解决方法：**
```js
// 表格勾选的时候执行的方法
handleSelect(selection, row) {
    // selection 是勾选中的行数据，数组
    if (selection.length > 6) {
    this.$refs.multipleTable.clearSelection();   //清空所有勾选的选项
    selection.splice(0, 5).forEach(row=> {
        this.$refs.multipleTable.toggleRowSelection(row);  // 将这些行选中
    })
    // this.selections = selection.splice(0, 6);
    this.$message.error({message: '最多可勾选五条数据', center: true});
    return false;
    }
},
```

5. 切换标签，下面显示不同的页面组件

**解决方法：**  Vue的component组件（相当于vue-router，会在该位置展示对应的页面组件）
```js
<component is='组件的name'></component>
```

6. 利用正则，将文章中的所有的关键字都替换成带标签的并且改变颜色

**解决方法：**
```js
let keyReg = new RegExp("(?=>)[\\s\\S]*?(?=(<|$))", 'g');
for (var i = 0; i < keyData.length; i++) {
    //匹配>和<中间的内容，然后替换关键字
    that.strHtml = that.strHtml.replace(keyReg, function (me) {
    return me.replace(keyData[i].keywordName, `<span style="color: red; cursor: pointer" class="keywordClickClass" keywordId="${keyData[i].id}">${keyData[i].keywordName}</span>`)
})
}
```

7. 在replace中使用 //正则，在运行的时候没有问题，**在前端打包的时候会出现错误**；

8. 页面刷新出现nginx404的问题

**解决方法：** 加上if判断就好了
```js
location / {
    if ( !-e $request_filename ) {
    proxy_pass    http://localhost:61080;
    }
}
```

9. 数组对象中将对象的某个属性值相同的放到一起。可以进行排序。

```js
// 可以通过排序，他们相同的就放到了一起，但是中文的，英文的怎么排序呢？
const arr = [{name: '李艳玲'},{name: '陈杰'},{name: '陈杰'}]
arr.sort((a, b) => {
    return a.name.localeCompare(b.name);
})
```

10. 在平时练习的时候，配置Vue项目中的路由信息。发现路由引进的组件执行了，但是页面确实空白页面。

**解决方法：**一开始以为是对路由不是很熟悉，写错了。但是检查多次之后发现并没有什么问题。然后百度之后发现，在配置VueRouter对象的时候需要写 **routes**。

11. 谷歌浏览器会有记住密码的功能，当点击记住密码的时候，type = password 会自动填充密码。

**解决方法：**在写一个输入框，然后将它隐藏，同时将autocomplete=new-password。name=test将他们两个name设置成相同的。

