# 实习生总结-王奇林  
## ant-design 使用总结  
* antd 使用技巧  
    文档中的各个组件都有针对不同场景或者调用不同api实现的小demo，通过查看demo代码可以对组件更加深入了解  
    并且还提供了每个demo的在线调试地址，对于demo中的数据、api、处理方法有不懂的可以随时调试  
    在每个组件的最后都有api说明，api在什么时候使用，类型、参数等都有详细说明  
    虽然文档中没有对一些属性的类型做详细说明，但是可以通过其属性名或者demo中的数据操作可以看出其数据格式
* antd 文档问题  
    Form组件中 Form.create(options) options里的onValuesChange 类型为(props, changedValues, allValues) => void 根据源代码，这个钩子函数只有前面两个参数，没有第三个参数，即无法在“任一表单域的值发生改变时”获取所有表单域的值  
* antd 部分组件使用心得  
    1. setFields 设置一组输入控件的值与 Error  
        使用场景：对值进行校验时，校验出错误可以返回对该值进行反馈提示  
        使用方法：  
        ```js  
        validateFields((error, values, index) => {
            if (!error) {
                setTimeout(() => {
                if (values.user === 'yiminghe') {
                    setFields({
                    user: {
                        value: values.user,
                        errors: [new Error('forbid ha')],
                    },
                    });
                }
                }, 500);
                } else {
                    console.log('error', error, values);
                }
        });
        ```
    2. Table 表格行/列合并  
        表头只支持列合并，使用 column 里的 colSpan 进行设置  
        表格支持行/列合并，使用 render 里的单元格属性 colSpan 或者 rowSpan 设值为 0 时，设置的表格不会渲染  
        在column中配置时，可以利用render(text, record, index)方法中的参数进行精确渲染
    3. Upload 文件上传  
        accept： 可接受的文件限制 audio/*  video/*  image/* 但是这个限制不够，无法阻止用于上传非法文件格式  
        beforeUpload： 上传之前的钩子函数，参数为上传的文件，所以可以根据文件进行类型、大小限制，若返回 false 则停止上传，支持返回一个 Promise 对象，Promise 对象 reject 时则停止上传，resolve 时开始上传  
        fileList: 已经上传的文件列表（受控），不是控制上传列表的显示界面  
        showUploadList： 这个是用来控制是否显示上传历史列表
    4. 模态框的销毁  
        在项目中模态框会大量使用，一般都用他的属性visible控制显隐，但是这个属性只是控制“显示”“隐藏”，如果一次操作后不对其进行销毁，那么下次操作会显示上次操作的数据，比如在表模态框中使用表单，并且对其进行初始化赋值，initialValue只会在组件创建时赋值，不对其进行销毁，这个值是不会变的。  
        销毁方法两种：  
        - 在modal的容器组件中用布尔值控制组件的销毁和创建  
        - 使用 destroyOnClose 进行销毁
    5. 后续更新...  
## dva使用总结  
* Subscription  
    用于订阅一个数据源，然后根据条件 dispatch 需要的 action  
    数据源可以是当前的时间、服务器的 websocket 连接、keyboard 输入、geolocation 变化、history 路由变化等等  
    异步数据初始化  
    ```js
    app.model({
        subscriptions: {
            setup({ dispatch, history }) {
                history.listen(({ pathname }) => {
                    if (pathname === '/users') {
                        dispatch({
                            type: 'users/fetch',
                        });
                    }
                });
            },
        },
    });  
    ```
## 开发规范  
* 用户输入  
    对于用户输入字段，需要做一定的限制  
    用户本身时邪恶的，如果不做限制，直接提交到后端，可能会发生难以想想的结果。比如对用户输入的数量字段需要做精确度和整数位限制，搜索等输入框要做字符校验，防止sql注入等  
* 界面统一  
    在布局上一定要统一，特别是在协作开发时，如果界面布局、样式不统一，会造成界面花哨、凌乱的感觉，现在界面设计趋向于扁平、简洁，所以统一才能给用户良好的体验，并且这也是规范  
* 避免代码冗余  
    在开发过程中，其实是不断尝试的过程，对于熟悉的功能可以信手拈来，但是新的功能总是要尝试的，所以在这过程中会产生很多冗余代码，一些不用的方法，属性，注释等，这些都可能会造成内存浪费、运行效率下降等结果  
* 命名规范  
    项目中的models，service，props规范命名可以使整个项目的风格更加统一，犹如一个人开发出来的；并且也可以使项目更加容易维护和重构，别人读你的代码，因为是遵守同一规范，所以不存在“代沟”
## 开发经验总结  
* 在state里面存储的数据根据需要只存储需要的数据字段(除开list)即用户不会更改的字段(比如id)，虽然中间多了一步数据字段的过滤，但是可以节省内存消耗，同时也可以避免一下情况：  
如果存储数据的全部字段，那么中间执行修改的操作后，一般是把list重新加载，list自然就更新了，但是state中的其他数据没有更新（虽然可以调用reducer进行更新，但如果不存储需要更新的字段，也就没有必要了），造成在和更新后的数据校验时不相等的情况  
* 以前一直认为展示组件是基础，由展示**组件**组成容器组件，所以开发顺序就是先展示组件——>容器组件——>models——>services, 经过几次小demo的实践，发现如果这样做，一是因为组件都是无状态组件，没有自身的state，无法调试，必须依靠容器组件
## lodash使用总结  
* _.pick(object, [props])  
    使用场景：一般用于在对象中筛选自己需要的字段并返回一个新对象  
    使用方法： 
    ```js  
    var object = { 'a': 1, 'b': '2', 'c': 3 };
    _.pick(object, ['a', 'c']);
    // => { 'a': 1, 'c': 3 }
    ```  
* _.find(collection, [predicate=_.identity], [fromIndex=0])  
    使用场景：用于在collection集合中搜索一个对象或者一个对象的某个字段，返回第一个返回真值的第一个元素  
    使用方法:  
    ```js  
    var users = [
        { 'user': 'barney',  'age': 36, 'active': true },
        { 'user': 'fred',    'age': 40, 'active': false },
        { 'user': 'pebbles', 'age': 1,  'active': true }
    ];
    _.find(users, { 'age': 1, 'active': true });
    // => object for 'pebbles'
    ```
* _.map(collection, [iteratee=_.identity])
    使用场景： 遍历 collection（集合）中的每个元素后返回的结果并返回一个新数组，比如可以从collection中提取想要的对象属性值并组成一个新数组；或者对对象的属性值进行一些处理  
    使用方法：  
    ```js  
    function square(n) {
        return n * n;
    }
    _.map({ 'a': 4, 'b': 8 }, square);
    // => [16, 64]  

    var users = [
        { 'user': 'barney' },
        { 'user': 'fred' }
    ];
    _.map(users, 'user');
    // => ['barney', 'fred']
    ```
## 疑惑  
* 用户做一些修改、删除、添加等操作后直接进行重新向后台请求列表，以达到列表为最新的数据的目的，但是这样做一是每一次操作都会增加一次请求服务器次数；并且新添加的数据无法直观的展现在列表中，经常添加的数据还要去分页查找。  
为什么不直接添加数据成功后，在list数组中用unshift在头部添加到第一行，并且也可以结合动画强调这一新添的数据  
* 在容器组件中，删除处理方法中一般是这样做：dispatch一个删除操作，然后dispatch更新一些本地数据；这样做没有任何异步操作，如果删除操作失败，仍然更新了本地操作  
可以在model里的判断是否删除成功后再执行更新操作，但是这样会让model变得臃肿不堪  
也可以用dva2.x中的dispatch返回的Promise进行异步操作，dva1.x不支持