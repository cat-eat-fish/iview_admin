<template>
    <div>
        <p slot="title">
            <!-- <count-to :end="2534" count-class="count-text" unit-class="unit-class" :decimals="2"></count-to> -->
            
            <Button type="success" @click="addmodal = true"><Icon type="md-add" />添加管理员</Button>
        </p>
        <!-- 添加管理员 -->
        <Modal
            v-model="addmodal"
            title="添加管理员"
            @on-ok="ok"
            @on-cancel="cancel">
            <Form ref="formValidate" :model="formValidate" :rules="ruleValidate" :label-width="80">
                <FormItem label="用户名" prop="username">
                    <Input v-model="formValidate.username" placeholder="请输入您的登录用户名"  />
                </FormItem>
                <FormItem label="姓名" prop="name">
                    <Input v-model="formValidate.name" placeholder="请输入您的姓名" />
                </FormItem>
                <FormItem label="密码" prop="password">
                    <Input v-model="formValidate.password" placeholder="请输入您的登录密码" />
                </FormItem>
                <FormItem label="重复密码" prop="password2">
                    <Input v-model="formValidate.password2" placeholder="请重复输入您的登录密码" />
                </FormItem>
                <FormItem label="手机号码" prop="phone">
                    <Input v-model="formValidate.phone" placeholder="请输入您的联系方式" />
                </FormItem>
                <FormItem label="身份证号" prop="card">
                    <Input v-model="formValidate.card" placeholder="请输入您的身份证号" />
                </FormItem>
                <FormItem label="所在城市" prop="city">
                    <Cascader :data="citydata" v-model="formValidate.city" placeholder="请选择所在城市" ></Cascader>
                </FormItem>
                <FormItem label="性别" prop="gender">
                    <RadioGroup v-model="formValidate.gender">
                        <Radio label="male">男性</Radio>
                        <Radio label="female">女性</Radio>
                    </RadioGroup>
                </FormItem>
                
                <FormItem label="备注" prop="desc">
                    <Input v-model="formValidate.desc" type="textarea" :autosize="{minRows: 2,maxRows: 5}" placeholder="请输入一些备注吧..." />
                </FormItem>
                <FormItem>
                    <Button type="primary" @click="handleSubmit('formValidate')">提交</Button>
                    <Button @click="handleReset('formValidate')" style="margin-left: 8px">重置</Button>
                </FormItem>
            </Form>
        </Modal>
        <!-- 编辑管理员modal -->
        <Modal
            v-model="editmodal"
            title="编辑管理员"
            @on-ok="editok"
            @on-cancel="editcancel">
            <Form ref="editformValidate" :model="editformValidate" :rules="editruleValidate" :label-width="80">
                <FormItem label="用户名" prop="username">
                    <Input v-model="editformValidate.username" placeholder="请输入您的登录用户名"  />
                </FormItem>
                <FormItem label="姓名" prop="name">
                    <Input v-model="editformValidate.name" placeholder="请输入您的姓名" />
                </FormItem>
                <FormItem label="密码" prop="password">
                    <Input v-model="editformValidate.password" placeholder="请输入您的登录密码" />
                </FormItem>
                <FormItem label="手机号码" prop="phone">
                    <Input v-model="editformValidate.phone" placeholder="请输入您的联系方式" />
                </FormItem>
                <FormItem label="身份证号" prop="card">
                    <Input v-model="editformValidate.card" placeholder="请输入您的身份证号" />
                </FormItem>
                <FormItem label="所在城市" prop="city">
                    <Cascader :data="citydata" v-model="editformValidate.city" placeholder="请选择所在城市" ></Cascader>
                </FormItem>
                <FormItem label="性别" prop="gender">
                    <RadioGroup v-model="editformValidate.gender">
                        <Radio label="male">男性</Radio>
                        <Radio label="female">女性</Radio>
                    </RadioGroup>
                </FormItem>
                
                <FormItem label="备注" prop="desc">
                    <Input v-model="editformValidate.desc" type="textarea" :autosize="{minRows: 2,maxRows: 5}" placeholder="请输入一些备注吧..."  />
                </FormItem>
                <FormItem>
                    <Button type="primary" @click="handleSubmit('formValidate')">提交</Button>
                    <Button @click="edithandleReset('editformValidate')" style="margin-left: 8px">重置</Button>
                </FormItem>
            </Form>
        </Modal>
        
        <Table width="100%" height="720" :loading="loading" border :columns="columns2" :data="data"></Table>

        <Modal title="View Image" v-model="visible2">
            <div class="demo-upload-list" v-for="(item,index) in uploadList" :key="index">
                <template v-if="item.status === 'finished'">
                    <img :src="item.url">
                    <div class="demo-upload-list-cover">
                        <Icon type="ios-eye-outline" @click.native="handleView(item.name)"></Icon>
                        <Icon type="ios-trash-outline" @click.native="handleRemove(item)"></Icon>
                    </div>
                </template>
                <template v-else>
                    <Progress v-if="item.showProgress" :percent="item.percentage" hide-info></Progress>
                </template>
            </div>
            <Upload
                ref="upload"
                name="inputFile"
                :show-upload-list="false"
                :default-file-list="defaultList"
                :on-success="handleSuccess"
                :format="['jpg','jpeg','png']"
                :max-size="2048"
                :on-format-error="handleFormatError"
                :on-exceeded-size="handleMaxSize"
                :before-upload="handleBeforeUpload"
                multiple
                type="drag"
                action="/api/file/uploading"
                style="display: inline-block;width:58px;"
            >
                <div style="width: 58px;height:58px;line-height: 58px;">
                    <Icon type="ios-camera" size="20"></Icon>
                </div>
            </Upload>  
            <div v-if="file !== null">
                上传文件: {{ file.name }} 
                <Button type="text" @click="upload" :loading="loadingStatus">{{ loadingStatus ? '上传中' : '点击上传' }}</Button>
            </div>
        </Modal>
        
            <!-- <Modal title="View Image" v-model="visible">
                <img :src="'https://o5wwk8baw.qnssl.com/' + imgName + '/large'" v-if="visible" style="width: 100%">
            </Modal> -->
    </div>
</template>

<script>
import addressJson from "@/api/address.json"
import axios from 'axios'
import CountTo from '_c/count-to'
export default {
  components: {
    CountTo
  },
  name: 'admin_page',
  data () {
    return {
        citydata: [
            {
                value: '北京',label: '北京',
                children: [{value: '故宫',label: '故宫'},{value: '天坛',label: '天坛'},{value: '王府井',label: '王府井'}]
            }, 
            {
                value: '江苏',label: '江苏',
                children: [
                    {value: '南京',label: '南京',
                        children: [{value: '夫子庙',label: '夫子庙',}]
                    },
                    {value: '苏州',label: '苏州',
                        children: [{value: '拙政园',label: '拙政园',},{value: '狮子林',label: '狮子林',}]
                    }
                ],
            }
        ],
        formValidate: {username:'', name: '',password: "",password2:"",phone:"",card:"", gender: '',city:[],desc: ''},
        editformValidate: {username:'', name: '',password: "",phone:"",card:"", gender: '',city:[],desc: ''},
        ruleValidate: {
            username: [{ required: true, message: '用户名不能为空', trigger: 'blur' }],
            // name: [{ required: true, message: '姓名不能为空', trigger: 'blur' }],
            password:[{ required: true, message: '登录密码不能为空', trigger: 'blur' }],
            password2:[{ required: true, message: '重复登录密码不能为空', trigger: 'blur' }],
            phone: [{ required: true, message: '联系方式不能为空', trigger: 'blur' }],
            // city: [{ required: true, message: '请选择所在城市', trigger: 'blur' }],
            // gender: [
            //     { required: true, message: 'Please select gender', trigger: 'change' }
            // ],
            // desc: [
            //     { required: true, message: '备注信息不能为空', trigger: 'blur' },
            //     { type: 'string', min: 20, message: '最少输入20个字', trigger: 'blur' }
            // ]
        },
        editruleValidate: {
            username: [{ required: true, message: '用户名不能为空', trigger: 'blur' }],
            // name: [{ required: true, message: '姓名不能为空', trigger: 'blur' }],
            password:[{ required: true, message: '登录密码不能为空', trigger: 'blur' }],
            phone: [{ required: true, message: '联系方式不能为空', trigger: 'blur' }],
            // address: [{ required: true, message: '请选择所在城市', trigger: 'blur' }],
            // gender: [
            //     { required: true, message: 'Please select gender', trigger: 'change' }
            // ],
            // desc: [
            //     { required: true, message: '备注信息不能为空', trigger: 'blur' },
            //     { type: 'string', min: 20, message: '最少输入20个字', trigger: 'blur' }
            // ]
        },
        defaultList: [
            // {'name': 'bc7521e033abdd1e92222d733590f104','url': 'https://o5wwk8baw.qnssl.com/bc7521e033abdd1e92222d733590f104/avatar'}
        ],
        imgName: '',
        visible: false,
        addmodal: false,
        editmodal:false,
        visible2: false,
        uploadList: [],
        file: null,
        loadingStatus: false,
        loading:true,
        end: 0,
        columns2: [
            {title: 'ID',key: 'id',width: 80,fixed: 'left',align: 'center',sortable: true},
            {title: '用户名',key: 'username',width: 160,align: 'center',fixed: 'left',
                render: (h, params) => {
                    if (params.row.$isEdit) {
                        return h('input', {
                            domProps: {value: params.row.username},
                            attrs: {style: 'width: 100%'},
                            on: {
                                input: function (event) {
                                params.row.username = event.target.value
                            }
                        }
                    });
                    } else  {
                        return h('div', params.row.username);
                    }
                }
            },
            {title: '姓名',key: 'name',width: 150,align: 'center',fixed: 'left',
                render: (h, params) => {
                    if (params.row.$isEdit) {
                        return h('input', {
                            domProps: {value: params.row.name},
                            attrs: {style: 'width: 100%'},
                            on: {
                                input: function (event) {
                                params.row.name = event.target.value
                            }
                        }
                    });
                    } else  {
                        return h('div', params.row.name);
                    }
                }
            },
            {title: '密码',key: 'password',width: 160,align: 'center',
                render: (h, params) => {
                    if (params.row.$isEdit) {
                        return h('input', {
                            domProps: {value: params.row.password},
                            attrs: {style: 'width: 100%'},
                            on: {
                                input: function (event) {
                                params.row.password = event.target.value
                            }
                        }
                    });
                    } else  {
                        return h('div', params.row.password);
                    }
                }
            },
            
            {title: '联系方式',key: 'phone',width: 160,align: 'center',
                render: (h, params) => {
                    if (params.row.$isEdit) {
                        return h('input', {
                            domProps: {value: params.row.phone},
                            attrs: {style: 'width: 100%'},
                            on: {
                                input: function (event) {
                                params.row.phone = event.target.value
                            }
                        }
                    });
                    } else  {
                        return h('div', params.row.phone);
                    }
                }
            },
            {title: '头像',key: "thumb",width: 80,align: 'center',     
                render: (h,params) => {
                    var _this = this;
                    // if(params.row.$isEdit){
                    //     return h('div',
                    //         {},[
                    //             h('Upload',{
                    //                 domProps:{multiple:false,},
                    //                 attrs:{action:"/api/file/uploading",name:"inputFile"},
                    //                 props:{"before-upload":this.handleUpload,},
                    //                 },[
                    //                     h('Button',{
                    //                         props: {type: 'primary',shape: 'circle',icon: 'ios-cloud-upload-outline',},
                    //                     },"上传"),
                    //                 ]
                    //             ),
                    //             h('div',{
                    //                 on:{click(){console.log(_this)}},
                    //             },), 
                    //         ],
                    //     )
                    // }else{
                        return h('img', {
                            props: {type: 'primary',size: 'small'},
                            attrs: {src: params.row.thumb, style: 'width: 40px;height: 40px;border-radius: 2px;'},
            　　　　　　　　 style: {marginRight: '5px'},
                            on:{click(e){
                                _this.visible2=true;
                                var thumb = params.row.thumb.replace("http","https");
                                _this.defaultList= [{name:"",url : thumb}];
                            }}
            　　　　　　 });
                    // }
    　　　　     }
            },
            {title: '注册时间',key: 'date',width: 180,align: 'center',
                // render: (h, params) => {
                //     if (params.row.$isEdit) {
                //         return h('DatePicker', {
                //             domProps: {value: params.row.date,type:"date"},
                //             attrs: {value:params.row.date, type:"date",placeholder:"选择日期",style: 'width: 100%'},
                //             on: {
                //                 input: function (event) {
                //                     var Y = event.getFullYear() < 10? "0"+event.getFullYear() : event.getFullYear();
                //                     var M = event.getMonth()+1 < 10? "0"+event.getMonth()+1 : event.getMonth()+1; 
                //                     var D = event.getDate() < 10? "0"+event.getDate() : event.getDate();
                //                     params.row.date = `${Y}-${M}-${D}`
                //                 }
                //             }
                //         });
                //     } else  {
                //         return h('div', params.row.date);
                //     }
                // }
            },
            {title: '注册地点',key: 'registeraddress',width: 180,align: 'center',},
            {title: '身份证号',key: 'card',width: 180,align: 'center',},
            {title: '城市',key: 'address',width: 250,align: 'center',
                
            },
            {title: '金额(元)',key: 'money',width: 100,sortable: true,align: 'center',},
            {title: '操作',key: 'action',width: 200,align: 'center',fixed: 'right',
                render: (h, params) => {
                    return h('div', [
                        h('Button', {
                            props: {type: 'primary',size: 'small'},
                            style: {marginRight: '5px'},
                            on: {click: () => {this.show(params.index)}}
                        }, '查看'),
                        h('Button', {
                            props: {type: 'success',size: 'small'},
                            style: {marginRight: '5px'},
                            on: {click: () => {
                                    // if (params.row.$isEdit) {this.handleSave(params.row)} else {this.handleEdit(params.row)}
                                    params.row.city = params.row.address.split("-");
                                    this.editformValidate = params.row
                                    this.editmodal = true;
                                }
                            }
                        // }, params.row.$isEdit ? '保存' : '编辑'),
                        },"编辑"),

                        h('Poptip', {
                            props: { placement:"bottom", confirm:true,title:"你确定要删除该项吗？",},
                            
                            on: {
                                click: () => {},
                                "on-ok":()=>{console.log(1)},
                                "on-cancel":()=>{console.log(0)}
                                }
                        }, [
                            h("Button",{
                                props:{type: 'error',size: 'small',}
                            },"删除")
                        ])
                    ]);
                }
            }
        ],
        data: [
                    
        ]
    }
  },
  computed:{
      addressdata:function(){
          var e = addressJson.citylist;
          var city = {};
          var p = [];
            if(e.length !== 0){
                e.map((item,index)=>{
                    p[index]={};
                    p[index].value = item.value;
                    p[index].label = item.value;
                    p[index].children = [];
                    if(item.c){
                        item.c.map((item2,index2)=>{
                            p[index].children[index2] = {};
                            p[index].children[index2].value = item2.n;
                            p[index].children[index2].label = item2.n;
                            p[index].children[index2].children = [];
                            if(item2.a){
                                item2.a.map((item3,index3)=>{
                                    p[index].children[index2].children[index3] = [];
                                    p[index].children[index2].children[index3].value = item3.s;
                                    p[index].children[index2].children[index3].label = item3.s;
                                })
                            }
                        })
                    }
                })
            }
            // city.p = p;
          return p
      }
  },
  mounted(){
    //   this.uploadList = this.$refs.upload.fileList;
    //    let _this = this
    //    var files = e.target.files[0]
    //    if (!e || !window.FileReader) return  // 看支持不支持FileReader
    //    let reader = new FileReader()
    //    console.log(reader)
    //    reader.readAsDataURL(files) // 这里是最关键的一步，转换就在这里
    //    reader.onloadend = function () {
    //      _this.src = this.result
    //    }
  },
  created(){
      this.citydata = this.addressdata;
    var that = this
    axios({method: 'post',url: '/api/admin'})
    .then(function(res){
        var data = res.data;
        data.map((item,index)=>{
            item.thumb = "http://localhost:3000"+(item.thumb.substr(8))
        })
        that.data = data;
        that.loading = false;
    }).catch(function(error){
        console.log(error)
    })
  },
  methods: {
      getType(obj) {
          var type = typeof obj;
          if (type !== 'object') {
            return type;
          }
          //如果不是object类型的数据，直接用typeof就能判断出来
          //如果是object类型数据，准确判断类型必须使用Object.prototype.toString.call(obj)的方式才能判断
          return Object.prototype.toString.call(obj).replace(/^[object (S+)]$/, '$1');
        },
    recursion(e){
        
    },
    show (index) {
        this.$Modal.info({
            title: '用户信息',
            content: `用户ID：${this.data[index].id}<br>用户名：${this.data[index].username}<br>姓名：${this.data[index].name}<br>地址：${this.data[index].address}<br>注册时间：${this.data[index].date}<br>身份证号：${this.data[index].card}<br>金额：${this.data[index].money}`
        })
    },
    remove (index) {
        this.data.splice(index, 1);
    },
    handleEdit (row) {
        this.$set(row, '$isEdit', true)
    }, 
    handleSave (row) {
        this.$set(row, '$isEdit', false)
    },
    handleUpload (file) {
        this.file = file;
        return false;
    },
    upload () {
        this.$refs.upload.post(this.file);
        this.loadingStatus = true;
        setTimeout(() => {
            this.file = null;
            this.loadingStatus = false;
        }, 1500);
    },
    count:function(){

    },
    handleView (name) {this.imgName = name;this.visible = true;},
    handleRemove (file) {const fileList = this.$refs.upload.fileList;this.$refs.upload.fileList.splice(fileList.indexOf(file), 1);},
    handleSuccess (res, file) {
        var that = this;
        if(res.status === 200){
            axios({
                method: 'post',
                url: '/api/admin'
            }).then(function(res){
                var data = res.data;
                data.map((item,index)=>{
                    item.thumb = "http://localhost:3000"+(item.thumb.substr(8))
                })
                that.data = data;
                that.loading = false;
                that.$Message.success(res.message)
                that.visible2 = false;
            }).catch(function(error){
                console.log(error)
                that.visible2 = false;
                that.$Message.success("success")
            })
            
        }
    },
    handleFormatError (file) {
        this.$Notice.warning({
            title: '文件格式不正确',
            desc: '文件格式 ' + file.name + ' 不正确，请选择jpg或png。'
        });
    },
    handleMaxSize (file) {
        this.$Notice.warning({
            title: '超出文件大小限制',
            desc: '文件 ' + file.name + ' 太大，不超过2M。'
        });
    },
    handleBeforeUpload (file) {
        this.file = file;
        return false;
    },
    editok(){},
    editcancel(){},
    ok () {this.$Message.info('Clicked ok');},
    cancel () {this.$Message.info('Clicked cancel');},
    handleSubmit (name) {
        this.$refs[name].validate((valid) => {
            if (valid) {
                this.$Message.success('Success!');
            } else {
                this.$Message.error('Fail!');
            }
        })
    },
    handleReset (name) {this.$refs[name].resetFields();},
    edithandleReset (name) {this.$refs[name].resetFields();}
  }
}
</script>

<style>
    .demo-upload-list{
        display: inline-block;
        width: 60px;
        height: 60px;
        text-align: center;
        line-height: 60px;
        border: 1px solid transparent;
        border-radius: 4px;
        overflow: hidden;
        background: #fff;
        position: relative;
        box-shadow: 0 1px 1px rgba(0,0,0,.2);
        margin-right: 4px;
    }
    .demo-upload-list img{
        width: 100%;
        height: 100%;
    }
    .demo-upload-list-cover{
        display: none;
        position: absolute;
        top: 0;
        bottom: 0;
        left: 0;
        right: 0;
        background: rgba(0,0,0,.6);
    }
    .demo-upload-list:hover .demo-upload-list-cover{
        display: block;
    }
    .demo-upload-list-cover i{
        color: #fff;
        font-size: 20px;
        cursor: pointer;
        margin: 0 2px;
    }
</style>
