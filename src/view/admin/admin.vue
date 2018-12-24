<template>
    <div>
        <p slot="title">
            <Icon type="ios-analytics"></Icon>
            管理员管理
            <count-to :end="2534" count-class="count-text" unit-class="unit-class" :decimals="2"></count-to>
        </p>
        
        <Table width="100%" height="720" :loading="loading" border :columns="columns2" :data="data"></Table>
    </div>
</template>

<script>
import axios from 'axios'
import { userData } from '@/api/userdata'
import CountTo from '_c/count-to'
export default {
  components: {
    CountTo
  },
  name: 'admin_page',
  data () {
    return {
        file: null,
        loadingStatus: false,
        loading:true,
        end: 0,
        columns2: [
            {title: 'ID',key: 'uid',width: 80,fixed: 'left',align: 'center',sortable: true},
            {title: '用户名',key: 'username',width: 160,align: 'center',fixed: 'left',
                render: (h, params) => {
                    if (params.row.$isEdit) {
                        return h('input', {
                            domProps: {value: params.row.username},
                            attrs: {style: 'width: 95%'},
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
            {title: '姓名',key: 'name',width: 80,align: 'center',fixed: 'left',
                render: (h, params) => {
                    if (params.row.$isEdit) {
                        return h('input', {
                            domProps: {value: params.row.name},
                            attrs: {style: 'width: 95%'},
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
            {title: '头像',key: "thumb",width: 180,align: 'center',     
                render: (h,params) => {
                    var _this = this;
                    if(params.row.$isEdit){
                        return h('div',
                            {

                            },[
                                h('Upload',{
                                    domProps:{multiple:false,},
                                    attrs:{action:"/api/file/uploading",name:"inputFile"},
                                    props:{"before-upload":this.handleUpload,},
                                    },[
                                        h('Button',{
                                            props: {type: 'primary',shape: 'circle',icon: 'ios-cloud-upload-outline',},
                                        },"上传"),
                                    ]
                                ),
                                h('div',{
                                    on:{click(){console.log(_this)}},
                                },["123",
                                    {
                                        render:(h,params)=>{
                                            var _this = this;
                                            if(_this.file !== null){
                                                return  h("Button",{
                                                    props: {type: 'primary',shape: 'circle',icon: 'ios-cloud-upload-outline',},
                                                },"ceshi1")
                                            }else{
                                                return h("Button",{
                                                    props: {type: 'primary',shape: 'circle',icon: 'ios-cloud-upload-outline',},
                                                },"ceshi2")
                                            }
                                        }   
                                    }
                                ]), 
                            ],
                        )
                    }else{
                        return h('img', {
                            props: {type: 'primary',size: 'small'},
                            attrs: {src: params.row.thumb, style: 'width: 40px;height: 40px;border-radius: 2px;'},
            　　　　　　　　  style: {marginRight: '5px'},
            　　　　　　 });
                    }
    　　　　     }
            },
            {title: '注册日期',key: 'date',width: 180,align: 'center',
                // render: (h, params) => {
                //     if (params.row.$isEdit) {
                //         return h('DatePicker', {
                //             domProps: {value: params.row.date,type:"date"},
                //             attrs: {value:params.row.date, type:"date",placeholder:"选择日期",style: 'width: 95%'},
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
                                    if (params.row.$isEdit) {this.handleSave(params.row)} else {this.handleEdit(params.row)}
                                }
                            }
                        }, params.row.$isEdit ? '保存' : '编辑'),
                        h('Button', {
                            props: {type: 'error',size: 'small'},
                            on: {click: () => {this.remove(params.index)}}
                        }, '删除')
                    ]);
                }
            }
        ],
        data: [
                    
        ]
    }
  },
  created(){
    var that = this
    axios({
        method: 'post',
        url: 'https://www.easy-mock.com/mock/5c1c431cc0d4cb7899468a5d/iview_admin/iview_admin'
    }).then(function(res){
        var data = res.data.data;
        that.data = data;
        that.loading = false;
    }).catch(function(error){
        console.log(error)
    })
  },
  methods: {
    show (index) {
        this.$Modal.info({
            title: '用户信息',
            content: `用户ID：${this.data[index].uid}<br>用户名：${this.data[index].username}<br>姓名：${this.data[index].name}<br>地址：${this.data[index].address}<br>注册时间：${this.data[index].date}<br>身份证号：${this.data[index].card}<br>金额：${this.data[index].money}`
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
    count:function(){

    },
    // dateFtt(date)   {  
    //     var Y = date.getFullYear();                 //月份
    //     var M = date.getMonth()+1;                 //月份   
    //     var D = date.getDate();                    //日   
        
    //     return Y+"-"+M+"-"+D;   
    // } 
    
  }
}
</script>

<style>

</style>
