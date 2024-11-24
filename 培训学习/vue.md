# 语法模板

除了el和data，其他重要属性：	

- methods: { 定义一些方法，可以在Vue和HTML中直接使用 }

- components: { 定义一些组件 }

- template: { 定义模板 }

- watch: { 定义观察属性 }

- computed: { 定义计算属性 }


```	js
new Vue({
		//这里是绑定某个div的id
        el: '#app',
        data: {
			//定义变量
            count: 1,
            users: [],
            imgstyle: {
                width: '100px'
            }
        },
        methods: {
			//方法
        }
    });
```











# 基本命令



#### v-on：绑定事件

```html
<input v-on:click="submit">
<!-- 语法糖 -->
<input @click="submit">
```

```html
<div id="app">
    <!-- `count`为 vue 里面定义的变量 -->
    <button v-on:click="count += 1">增加 1</button>
    <p>这个按钮被点击了 {{ counter }} 次。</p>
</div>
```

绑定方法

```html
<div id="app">
   <!-- `greet` 是在 vue 定义的方法名 -->
  <button v-on:click="greet">Greet</button>
</div>
```



###### 事件修饰符

```html
<!-- 阻止单击事件冒泡 -->
<a v-on:click.stop="doThis"></a>
<!-- 提交事件不再重载页面 -->
<form v-on:submit.prevent="onSubmit"></form>
<!-- 修饰符可以串联  -->
<a v-on:click.stop.prevent="doThat"></a> 
<!-- 只有修饰符 -->
<form v-on:submit.prevent></form>
<!-- 添加事件侦听器时使用事件捕获模式 -->
<div v-on:click.capture="doThis">...</div>
<!-- 只当事件在该元素本身（而不是子元素）触发时触发回调 -->
<div v-on:click.self="doThat">...</div>

<!-- click 事件只能点击一次，2.1.4版本新增 -->
<a v-on:click.once="doThis"></a>
```



###### 按键修饰符

- `.enter`
- `.tab`
- `.delete` (捕获 "删除" 和 "退格" 键)
- `.esc`
- `.space`
- `.up`
- `.down`
- `.left`
- `.right`
- `.ctrl`
- `.alt`
- `.shift`
- `.meta`

```html
<!-- 只有在按下 tab 时调用 vm.submit() -->
<input v-on:keyup.tab="submit">
```



#### v-text：绑定文本



#### v-html：绑定HTML文本



#### v-bind：绑定一个属性

语法糖：v-bind可以省略

```html
<!-- var1 为 vue 定义的变量,可以是一个也可以是键值对 -->
<input v-bind:href="var1">
<!-- 语法糖 -->
<input :href="var1">

<!-- 绑定行内样式，可以写多个 'color' 表示这个属性 color 表示这个属性的值，是在 vue 定义的变量 -->
<div :style="{'color':color}">绑定样式</div>
```







#### v-model：绑定模型【针对表单】

###### 输入框

```html
<!-- message 是在 vue 定义的变量 -->
<!-- 输入框的值绑定 message ，所有用到了这个变量的地方会随着输入框改变 -->
<input v-model="message">
<p>消息是: {{ message }}</p>
```



###### 单选框

单选下拉列表一样

```html
<!-- picked 是在 vue 定义的变量 -->
<!-- picked 初始值为 111 则默认选择 111 -->
<input type="radio" id="runoob" value="111" v-model="picked">
<label for="runoob">111</label>
<br>
<input type="radio" id="google" value="222" v-model="picked">
<label for="google">222</label>
<br>
<span>选中值为: {{ picked }}</span>
```



######  复选框 

多选下拉列表一样

```html
<!-- checkedNames 是在 vue 定义的变量，是一个数组 -->
<input type="checkbox" id="runoob" value="Runoob" v-model="checkedNames">
<label for="runoob">Runoob</label>
<input type="checkbox" id="google" value="Google" v-model="checkedNames">
<label for="google">Google</label>
<input type="checkbox" id="taobao" value="Taobao" v-model="checkedNames">
<label for="taobao">taobao</label>
<br>
<span>选择的值为: {{ checkedNames }}</span>
```







#### v-for循环

```html
<!-- users 是在 vue 定义的变量，是一个数组,里面可能是对象 -->
<!-- user 是每次从 users 里面拿出一个 -->
<!-- index 是第几个，从0开始 -->
<tr v-for="(user, index) in users">
    <td>{{index + 1}}</td>
    <td>{{user.id}}</td>
    <td>{{user.name}}</td>
    <td>{{user.money}}</td>
</tr>
```



#### v-if选择，单独使用或者结合v-else

```
	<div v-if="boolean">...</div>
		<div v-else>...</div>
		也可以使用多个v-else-if
```



#### v-show用于切换显示

```
		<div v-show="visible">...</div>
```











#### 组件



###### html

- 【:icon】 是组件内部【props】定义的属性，传入参数是外部【data】定义的属性
- 【@event】是组件内部【methods】定义的事件，名字由【this.$emit('event');】决定，传入参数为外部【methods】定义的方法

```html
<div id="app">
    <custom-button :icon="url" @event="clickEvent">按钮文字</custom-button>
</div>
```



###### js



```js
//定义组件
const customButton = {
    // props可以说和data属性完全一样的用法，但是在外部data不能使用
    props: ['icon'],
    template: '<div><img :src="icon" style="width: 100px;" @click="event()"/></div>',
    methods: {
        event: function () {
            console.log('点击了');
            this.$emit('event');
        },
    },
};
new Vue({
    el: '#app',
    //注册组件
    components: {
        customButton: customButton,
    },
    data:{
        url: '../a_img/拉克丝狗年限定.jpg',
    },
    methods: {
        clickEvent: function(){
            console.log('点击了外');
        },
    },
})
```











#### 路由和视图

###### js部分

```js
<script src="../a_js/vue-2.6.14.js"></script>
<script src="../a_js/vue-router.js"></script>
<script>
    // 定义主页视图
    const view1 = {
        props: ['name'],
        template: '<div><b>主页</b></div>'
    }
    // 定义详情页视图
    const view2 = {
        props: ['name'],
        template: '<div><img width="100" src="../a_img/拉克丝狗年限定.jpg" /></div>'
    }
    // 定义关于页视图
    const view3 = {
        props: ['name'],
        template: '<div><b>关于页</b></div>'
    }
    //1.定义路由
    const routes = [
        { name: 'index', path: '/index', component: view1 },
        { name: 'detail', path: '/detail', component: view2 },
        { name: 'concerning', path: '/concerning', component: view3 },
    ]
    //2.创建VueRouter对象
    const router = new VueRouter({
        // mode: 'hash',//默认值
        mode: 'history',//历史模式，可以模拟真实路径
        routes: routes,
    })
    new Vue({
        el: '#app',
        //3.将路由对象设置到vue实例中
        router: router,
        components: {
            view1: view1,
            view2: view2,
            view3: view3,
        },
        data: {},
        methods: {},
    });
</script>
```



###### html部分

```html
<div id="app">
    <h2>4.路由显示位置</h2>
    <div style="border: 1px solid red;">
        <router-view>路由组件显示位置</router-view>
    </div>
    <h2>5.指定超链接显示哪个视图</h2>
    <router-link to="/index">显示主页</router-link>
    <router-link to="/detail">显示详情页</router-link>
    <router-link to="/concerning">显示关于页</router-link>
</div>
```















# element组件

#### 表格

```html
<!--【:data】绑定的是js里面的数据-->
<el-table :data="tableData" style="width: 100%">
    
    <!--【prop】：这个表格【:data】绑定的数据里面的属性名-->
    <!--【label】：这一列的表头-->
    <!--【width】：设置之后固定宽度-->
    <el-table-column prop="date" label="日期" width="180">
    </el-table-column>
    <el-table-column prop="name" label="姓名" width="180">
    </el-table-column>
    <!--【show-overflow-tooltip】：自适应宽度他会沾满省下的-->
    <el-table-column prop="address" label="地址" show-overflow-tooltip>
    </el-table-column>
    
</el-table>
```

```js
new Vue({
        el: '#app',
        data: {
            //绑定整个表格的数据
            tableData: [],
            },
        }）
```



###### 添加多选框

```html
<el-table-column type="selection" width="55">
</el-table-column>
```



###### 给列添加排序

在【el-table-column】添加【sortable】

```html
<el-table-column prop="date" label="日期" width="180" sortable>
</el-table-column>
```



###### 给行添加点击事件

【el-table】添加【@selection-change="handleSelectionChange" 】

传参：【@selection-change="handleSelectionChange( scope.$index, scope.row )" 】

scope.$index：下标

 scope.row：这一行的数据

```html
<el-table :data="tableData" style="width: 100%" 
          @selection-change="handleSelectionChange" >
```



###### 给行添加按钮

```html
<el-table-column label="操作">
    <template slot-scope="scope">
        <el-button size="mini" @click="handleEdit(scope.$index, scope.row)">
            编辑</el-button>
        <el-button size="mini" type="danger"  
                   @click="handleDelete(scope.$index, scope.row)">删除</el-button>
    </template>
</el-table-column>
```







#### 表单

```html
<!--【:model】：绑定表单数据-->
<!--【:rules】：表单验证规则-->
<!--【:ref】：绑定控件方便表单验证获取-->
<el-form :model="addForm" :rules="rules" ref="addForm" label-width="80px">

    <!--【label】：表单行的说明-->
    <!--【:label-width】：表单行说明的宽度-->
    <!--【prop】：表单行绑定的验证规则的名字-->
    <el-form-item label="名字" :label-width="formLabelWidth" prop="name">
        <!--【v-model】：表单绑定表单数据的属性-->
        <el-input v-model="addForm.name" autocomplete="off"></el-input>
    </el-form-item>
    <el-form-item label="时间" :label-width="formLabelWidth" prop="date">
        <el-date-picker type="date" placeholder="选择日期" v-model="addForm.date" style="width: 100%;"autocomplete="off"></el-date-picker>
    </el-form-item>
    <el-form-item label="地址" :label-width="formLabelWidth" prop="address">
        <el-input v-model="addForm.address" autocomplete="off"></el-input>
    </el-form-item>

    <el-form-item>
        <el-button type="primary" @click="addOk('addForm')">确 定</el-button>
        <el-button @click="resetForm('addForm')">重置</el-button>
        <el-button @click="addNo('addForm')">取 消</el-button>
    </el-form-item>

</el-form>
```

```vue
//绑定的验证规则
date:{
rules: {
name: [
{ required: true, message: '请输入名字', trigger: 'blur' },
{ min: 2, max: 5, message: '长度在 2 到 5 个字符', trigger: 'blur' }
],
date: [
{ type: 'date', required: true, message: '请选择日期', trigger: 'change' }
],
address: [
{ required: true, message: '请填写地点', trigger: 'blur' }
],
},
}

//绑定的方法
methods: {

//重置
resetForm(formName) {
this.$refs[formName].resetFields();
},

/* 确定添加数据 */
addOk(formName) {
this.$refs[formName].validate((valid) => {
if (valid) {
//把表单数据赋值给data
let data = {
name: this.addForm.name,
date: this.addForm.date.Format("yyyy-MM-dd"),
address: this.addForm.address,
}
//存入表格数组
this.tableData.push(data)
//清除表单绑定的数据
Object.keys(this.addForm).forEach(key => this.addForm[key] = '');
this.resetForm(formName)
//提示
this.$notify({
title: '成功',
message: '添加数据成功',
type: 'success'
});
} else {
console.log('error submit!!');
return false;
}
});
},

/* 取消添加数据 */
addNo(formName) {
//清除表单绑定的数据
Object.keys(this.addForm).forEach(key => this.addForm[key] = '');
this.resetForm(formName)
//提示
this.$notify.info({
title: '消息',
message: '您取消了添加'
});
},
}
```











#### 文件上传

```html
 <!--【action】：文件上传的路径-->
 <!--【:auto-upload】：文件是否上传-->
<el-upload action="updateFile" list-type="picture-card" :auto-upload="true" name="file">
    <i slot="default" class="el-icon-plus"></i>
    <div slot="file" slot-scope="{file}">
        <img class="el-upload-list__item-thumbnail" :src="file.url" alt="">
        <span class="el-upload-list__item-actions">
            <span class="el-upload-list__item-preview" @click="handlePictureCardPreview(file)">
                <i class="el-icon-zoom-in"></i>
            </span>
            <span v-if="!disabled" class="el-upload-list__item-delete" @click="handleDownload(file)">
                <i class="el-icon-download"></i>
            </span>
            <span v-if="!disabled" class="el-upload-list__item-delete" @click="handleRemove(file)">
                <i class="el-icon-delete"></i>
            </span>
        </span>
    </div>
</el-upload>
<el-dialog :visible.sync="dialogVisible">
    <img width="100%" :src="dialogImageUrl" alt="">
</el-dialog>
```







#### 布局容器

```html
<el-container>
    <!-- 头部 -->
    <el-header>
    </el-header>
    <el-container>
        <el-aside width="150px">边边</el-aside>
        <!-- 主体部分 -->
        <el-main>
            主体部分
        </el-main>
        <el-aside width="150px">边边</el-aside>
    </el-container>
    <!-- 底部部分 -->
    <el-footer>
        底部
    </el-footer>
</el-container>
```











#### 布局

【type="flex"】开启flex布局

- 【justify="center"】水平对齐方式，可选值： start/end/center/space-around/space-between 
- 【align="bottom"】垂直对齐方式，可选值： top/middle/bottom （垂直对齐方式需设置el-row的高度）

【:gutter="20"】每个单元格的间隔像素

```html
<el-row type="flex"  justify="center" align="bottom" :gutter="20">
    <el-col :span="6">
        <div class="grid-content bg-purple"></div>
    </el-col>
    <el-col :span="6">
        <div class="grid-content bg-purple"></div>
    </el-col>
    <el-col :span="6">
        <div class="grid-content bg-purple"></div>
    </el-col>
    <el-col :span="6">
        <div class="grid-content bg-purple"></div>
    </el-col>
</el-row>
```



























