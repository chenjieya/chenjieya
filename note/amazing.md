1. 上传文件
  
   - new formData（）构建请求体， formData.append('参数名', 二进制文件名)，可以通过input的files属性获取（参数名字是和后端商量的）
   
2. 网络请求-**字段**（field）

   - **字段**代表的是返回数据中的属性的名字

   ```json
   {
       status: 200,
       data: [
           {id: '123456'},
           {name: 'chenjie'},
           {age: 18},
           {sex: 'male'},
       ],
   }
   
   //比如说上面这个是返回的数据结构，数据里面的属性就是字段
   ```

   - 字段必须和表格头相对应

     >每一列的字段，会找到数据中对应的字段

3. 开发中遇到的**v-if问题**

   - 情景模拟：有一个select下拉框，还有一个文本输入框，这两个输入框分别在不同的状态下显示。其中select下拉框里面的内容是通过获取到id动态插入进去的。

   ```vue
   <div class="layui-input-block" v-show='!readOnly'>
       <select id="form_contentType"></select>
   </div>
   <div v-if='readOnly'>
       <div class="layui-input-block">
           <input id="readContentType">
       </div>
   </div>
   <script>
   	var vm = new Vue({
           data() {
               return {
                   readOnly: true,   //控制变量
               }
           }
       });
   </script>
   
   
   ```

   

```javascript
// 伪代码(jquery)
// 拿到即将要渲染到下拉框中的内容，进行遍历。
// 在遍历的时候动态生成下拉框中的内容结构。
// 然后插入到select下拉框中，要想插入进去就要通过id获取到该元素，然后通过append等方法插入进去。

// 问题来了：为什么readOnly是true，我通过该方法渲染的下拉框，为什么渲染不出来？
```

```js
// 解决：
/**
	因为v-if是让该dom元素进行销毁创建的，在执行该方法的时候该元素不存在，获取不到id。改用v-show就好了。
**/
```



4. 联动菜单，在有的时候，需要传递父级的id，才能得到后面的数据。所以我们通常把select框的value设置成获取到的id值。（在这里补充一下题外话，label属性可以规定下拉框中显示的是什么数据）我们传递的value值id是获取联动下拉框数据的。但是我们在提交表单的时候需要提交的是输入框中的汉字。这要怎么弄呢？

   可以在select的change事件中传递id过来，然后和数据对比一下。返回数据中的name值。

   

5. 有的项目可能会封装一些全局组件， 这些全局组件之间进行传值。还有的时候兄弟组件之间需要传值。

   ```js
   //main.js  用到了事件总线event bus
   Vue.prototype.bus = new Vue();   //将vue实例挂载到原型上，每一个组件都可以使用bus方法
   ```

   ```js
   //组件  触发$emit('方法名'，参数)
   this.bus.$emit('transfer', data);
   ```

   ```js
   //兄弟组件 通过$on('方法名', 回调)
   this.bus.$on('方法名', (res)=> {
       console.log(res)  //这是穿过来的参数
   })
   ```

   

 6. 可能会有一些对象需要枚举，我们可以将他们写成一个class类，为什么要这样呢？这样的话，可以写一些方法，比如说，获取到所有的值，还有给你对应的值，取到对应的名字之类的。

    ```js
     class EsEnum {
      constructor(val) {
        if (!val) {
          return;
        }
        Object.keys(val).forEach(key => {
          let item = val[key];
          this.addAll(key, item);
        });
        return this;
      }
    
      /**
       * 添加枚举
       * @param key
       * @param item
       */
      addAll(key, item) {
        this[key] = item;
      }
    
      /**
       * 获取所有枚举值列表
       * @returns {Array}
       */
      getItemList() {
        let optionList = [];
        for (let i in this) {
          let item = this[i];
          let option = {};
          Object.assign(option, item);
          option.key = i;
          optionList.push(option);
        }
        optionList.sort((a, b) => {
          let value1 = a['index'];
          let value2 = b['index'];
          return value1 - value2
        });
        return optionList;
      }
    
      /**
       * 获取需要排除的枚举值后的所有枚举值列表
       * @param val 需要排除的枚举值或枚举值数组
       */
      getItemListExcept(val) {
        let valueArr = [];
        if (val) {
          if (Array.isArray(val)) {
            valueArr = val;
          } else {
            valueArr.push(val);
          }
        }
        let optionList = [];
        for (let i in this) {
          let item = this[i];
          if (!valueArr.includes(item.name)) {
            let option = {};
            Object.assign(option, item);
            option.key = i;
            optionList.push(option);
          }
        }
        return optionList;
      }
    
      /**
       * 根据name获取index
       * @param name
       */
      indexOfName(name) {
        if (name) {
          for (let i in this) {
            let e = this[i];
            if (e.name.toString() === name.toString()) {
              return (e.index);
            }
          }
        }
        return null;
      }
    
      /**
       * 根据index获取name
       * @param index
       * @returns {*}
       */
      nameOfIndex(index) {
        if (!index && index !== 0) {
          return '';
        }
        for (let i in this) {
          let e = this[i];
          if (e.index !== undefined && e.index !== null && e.index.toString() === index.toString()) {
            return e.name;
          }
        }
        return '';
      }
    }
    
    const fieldTypeEnum = new EsEnum({
        FIELD_TYPE_DEFAULT: {name: '默认', index: 1},
        FIELD_TYPE_DATE: {name: '日期', index: 2},
        FIELD_TYPE_ENUM: {name: '枚举', index: 3}
    });
    ```

    

7. 导出

   fetchAPI有一个blob主要是处理二进制文件的，解决返回是Promise的问题

   之后可以动态的创建a元素，然后他的url是重点，**新知识：url是通过URL.dreateObjectURL(blob)可以将blob对象转换成内存URL地址**如果要想设置导出文件的名字，可以给a元素自定义属性，设置完之后手动调用a的点击事件。（当点击完成之后需要URL.revokeObjectURL(blob) 来释放内存）。
   
8. **layui文件上传中遇到的问题**

   介绍一下我的问题，我们需要在上传文件的时候对下拉框进行必填校验，当时layui的校验属性并不管用。我通过获取input框中数值是否存在进行校验，但是校验如果没通过之后，需要做的就是阻止文件上传。（查了一些资料，全是改源码，然后直接return false就可以阻止了）

   **我的解决方法：**

   ​	写一个计算属性，判断一下获取到的数值，能不能通过校验，返回值为true和false。 然后我们在layer.upload.render()上传文件函数的before回调函数中，就是在上传文件之前的回调函数。判断一下计算属性的返回值，如果没有通过校验，我们就抛出错误。然后就阻止了文件上传。

   ```vue
   <template>
   	里面是上传文件的结构，上面有两个下拉框，中间有一个选择文件的框。
   </template>
   
   <script>
   	export default : {
           computed: {
           handleRequire() {
               var _this = this;
               const {factoryName, accountPeriod} = _this.searchParam;
               if (!factoryName || !accountPeriod) {
                   layerInfo('请选择工厂和账期', 2);
                   return false;
               }
               return true;
           },
       },
        // 下面是一个上传文件的方法，layui的。   bindAction绑定的是上传文件的确定按钮
           methods: {
               loadChooseFileCheckBoxFunc: function () {
               var _this = this;
               if (_this.uploadDom) {
                   return
               }
   
               _this.uploadDom = layuiEvent.upload.render({
                   elem: '#chooseFile',
                   url: _this.uploadFileUrl, //改成您自己的上传接口
                   auto: false,
                   accept: 'file', //普通文件
                   acceptMime: '.xls,.xlsx', //选择文件时，过滤掉这三种文件之外的所有文件
                   exts: 'xls|xlsx', //允许上传文件的格式
                   size: _this.maxFileSize * 1024, //限制文件大小，最大为50MB的文件，此处单位是KB
                   bindAction: '#test9',
                   choose: function (obj, index) {
                       _this.files = obj.pushFile(); //将每次选择的文件追加到文件队列
                       //读取本地文件
                       obj.preview(function (index, file, result) {
                           _this.uploadFileName = file.name;
                       });
                       _this.uploadDom.config.elem.next()[0].value = '';
                   },
                   done: function (res, index) {
                       if (res.ok) {
                           layerInfo("文件上传成功", 1);
                           $('#formrest')[0].reset();
                           layer.close(_this.layerBox);
                           //执行查询表格数据的方法
                           transfer.$emit('mainTableSearchDataFunc');
                       } else {
                           $('#formrest')[0].reset();
                           layer.alert(res.message)
                           layer.close(_this.layerBox);
                       }
                       //删除数组文件中上传成功的图片，防止重复上传（重点）
                       for (let x in  _this.files) {
                           delete _this.files[x];
                       }
                   },
                   before: function (){
                       if (!_this.handleRequire){
                           throw Error('抛出错误,阻止文件上传');
                       }
                       this.data = {
                           // id: '传入id值',
                           // tablename: '传入tablename的值'
                           factoryName: _this.searchParam.factoryName,
                           accountPeriod: _this.searchParam.accountPeriod,
                       }
                   },
                   error: function () {
                       //删除数组文件中上传成功的图片，防止重复上传（重点）
                       for (let x in  _this.files) {
                           delete _this.files[x];
                       }
                   }
               });
           },
           }
       } 
   </script>
   ```

   

9. 自己写的代码，months代表的是用户选择的月份，years代表的是用户选择的年份。实现的功能是根据用户选择的年月进行向后推。

   比如说：用户选择2021-6  然后显示 2021-7  2021-8  2021-9 2021-10  2021-11 2021-12  2022-1 。。。。等

```js
(month == undefined ? 'N+1' : (months + 1) % 12 == 0? years + "-" +12 : (months + 1) > 12 ? (years+1)+'-'+(months + 1) % 12 : years + '-' +  (months + 1) % 12) +'月备料数'
```


10. js获取本机网络ip的方法

```html
<!doctype html>
<html><head>
    <meta charset="utf-8">
    <title>Network IP Address via ipcalf.com</title>
</head><body>
Your network IP is: <h1 id=list>-</h1> Make the locals proud.
<script>

// NOTE: window.RTCPeerConnection is "not a constructor" in FF22/23
var RTCPeerConnection = /*window.RTCPeerConnection ||*/ window.webkitRTCPeerConnection || window.mozRTCPeerConnection;

if (RTCPeerConnection) (function () {
    var rtc = new RTCPeerConnection({iceServers:[]});
    if (1 || window.mozRTCPeerConnection) {      // FF [and now Chrome!] needs a channel/stream to proceed
        rtc.createDataChannel('', {reliable:false});
    };
    
    rtc.onicecandidate = function (evt) {
        // convert the candidate to SDP so we can run it through our general parser
        // see https://twitter.com/lancestout/status/525796175425720320 for details
        if (evt.candidate) grepSDP("a="+evt.candidate.candidate);
    };
    rtc.createOffer(function (offerDesc) {
        grepSDP(offerDesc.sdp);
        rtc.setLocalDescription(offerDesc);
    }, function (e) { console.warn("offer failed", e); });
    
    
    var addrs = Object.create(null);
    addrs["0.0.0.0"] = false;
    function updateDisplay(newAddr) {
        if (newAddr in addrs) return;
        else addrs[newAddr] = true;
        var displayAddrs = Object.keys(addrs).filter(function (k) { return addrs[k]; });
        document.getElementById('list').textContent = displayAddrs.join(" or perhaps ") || "n/a";
    }
    
    function grepSDP(sdp) {
        var hosts = [];
        sdp.split('\r\n').forEach(function (line) { // c.f. http://tools.ietf.org/html/rfc4566#page-39
            if (~line.indexOf("a=candidate")) {     // http://tools.ietf.org/html/rfc4566#section-5.13
                var parts = line.split(' '),        // http://tools.ietf.org/html/rfc5245#section-15.1
                    addr = parts[4],
                    type = parts[7];
                if (type === 'host') updateDisplay(addr);
            } else if (~line.indexOf("c=")) {       // http://tools.ietf.org/html/rfc4566#section-5.7
                var parts = line.split(' '),
                    addr = parts[2];
                updateDisplay(addr);
            }
        });
    }
})(); else {
    document.getElementById('list').innerHTML = "<code>ifconfig | grep inet | grep -v inet6 | cut -d\" \" -f2 | tail -n1</code>";
    document.getElementById('list').nextSibling.textContent = "In Chrome and Firefox your IP should display automatically, by the power of WebRTCskull.";
}

</script>

</body></html>
```

