# 实习生培训总结-王奇林  
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
    4. 后续更新...
## 开发规范  
* 用户输入  
    对于用户输入字段，需要做一定的限制  
    用户本身时邪恶的，如果不做限制，直接提交到后端，可能会发生难以想想的结果。比如对用户输入的数量字段需要做精确度和整数位限制，搜索等输入框要做字符校验，防止sql注入  
* 界面统一  
    在布局上一定要统一，特别是在协作开发时，如果界面布局、样式不统一，会造成界面花哨、凌乱的感觉，现在界面设计趋向于扁平、简洁，所以统一才能给用户良好的体验，并且这也是规范  
* 避免代码冗余  
    在开发过程中，其实是不断尝试的过程，对于熟悉的功能可以信手拈来，但是新的功能总是要尝试的，所以在这过程中会产生很多冗余代码，一些不用的方法，属性，注释等，这些都可能会造成内存浪费、运行效率下降等结果  
