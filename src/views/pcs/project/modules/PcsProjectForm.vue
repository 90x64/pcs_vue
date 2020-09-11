<template>
  <a-spin :spinning="confirmLoading">
    <j-form-container :disabled="formDisabled">
      <a-form :form="form" slot="detail">
        <a-row>
          <a-col :span="12">
            <a-form-item label="项目名称" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['projectName']" placeholder="请输入项目名称"></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="项目编码" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['projectNo']" placeholder="请输入项目编码"></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="项目日期" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-date placeholder="请选择项目日期" v-decorator="['startingDate']" :trigger-change="true" style="width: 100%"/>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <!--
            <a-form-item label="项目经理" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-search-select-tag v-decorator="['projectManager']" dict="sys_user left join sys_user_role ON sys_user.id = sys_user_role.user_id left join sys_role on sys_user_role.role_id = sys_role.id where sys_user.del_flag = 0 and sys_user.status = 1 and sys_role.role_code = 'PCS',sys_user.username,sys_user.username" />
            </a-form-item>
            -->
            <a-form-item label="项目经理" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['projectManager']" placeholder="请输入项目经理"></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="工程承建单位" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-search-select-tag v-decorator="['company']" dict="sys_dict left join sys_dict_item on sys_dict.id = sys_dict_item.dict_id where sys_dict.del_flag = 0 and sys_dict.dict_code = 'company' and sys_dict_item.`status` = 1,sys_dict_item.item_value,sys_dict_item.item_value" />
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="工程实施单位" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-search-select-tag v-decorator="['constructionUnit']" dict="sys_dict left join sys_dict_item on sys_dict.id = sys_dict_item.dict_id where sys_dict.del_flag = 0 and sys_dict.dict_code = 'construction_unit' and sys_dict_item.`status` = 1,sys_dict_item.item_value,sys_dict_item.item_value" />
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="施工日期" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-date placeholder="请选择施工日期" v-decorator="['construtionDate']" :trigger-change="true" style="width: 100%"/>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="联系电话" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['contactNumber']" placeholder="请输入联系电话"></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="类型" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['projectStatus']" placeholder="请输入类型"></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="管线" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['pipeline']" placeholder="请输入管线"></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="站控" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['station']" placeholder="请输入站控"></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="ip1" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['ip1']" placeholder="请输入ip1"></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="ip2" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['ip2']" placeholder="请输入ip2"></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="ip3" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['ip3']" placeholder="请输入ip3"></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="ip4" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['ip4']" placeholder="请输入ip4"></a-input>
            </a-form-item>
          </a-col>
          <a-col v-if="showFlowSubmitButton" :span="24" style="text-align: center">
            <a-button @click="submitForm">提 交</a-button>
          </a-col>
        </a-row>
      </a-form>
    </j-form-container>
  </a-spin>
</template>

<script>

  import { httpAction, getAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import { validateDuplicateValue } from '@/utils/util'
  import JFormContainer from '@/components/jeecg/JFormContainer'
  import JDate from '@/components/jeecg/JDate'  
  import JSearchSelectTag from '@/components/dict/JSearchSelectTag'

  export default {
    name: 'PcsProjectForm',
    components: {
      JFormContainer,
      JDate,
      JSearchSelectTag,
    },
    props: {
      //流程表单data
      formData: {
        type: Object,
        default: ()=>{},
        required: false
      },
      //表单模式：true流程表单 false普通表单
      formBpm: {
        type: Boolean,
        default: false,
        required: false
      },
      //表单禁用
      disabled: {
        type: Boolean,
        default: false,
        required: false
      }
    },
    data () {
      return {
        form: this.$form.createForm(this),
        model: {},
        labelCol: {
          xs: { span: 24 },
          sm: { span: 5 },
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 16 },
        },
        confirmLoading: false,
        validatorRules: {
        },
        url: {
          add: "/pcsProject/add",
          edit: "/pcsProject/edit",
          queryById: "/pcsProject/queryById"
        }
      }
    },
    computed: {
      formDisabled(){
        if(this.formBpm===true){
          if(this.formData.disabled===false){
            return false
          }
          return true
        }
        return this.disabled
      },
      showFlowSubmitButton(){
        if(this.formBpm===true){
          if(this.formData.disabled===false){
            return true
          }
        }
        return false
      }
    },
    created () {
      //如果是流程中表单，则需要加载流程表单data
      this.showFlowData();
    },
    methods: {
      add () {
        this.edit({});
      },
      edit (record) {
        this.form.resetFields();
        this.model = Object.assign({}, record);
        this.visible = true;
        this.$nextTick(() => {
          this.form.setFieldsValue(pick(this.model,'createBy','createTime','updateBy','updateTime','sysOrgCode','projectName','projectNo','company','pipeline','station','projectStatus','construtionDate','startingDate','constructionUnit','projectManager','contactNumber','ip1','ip2','ip3','ip4'))
        })
      },
      //渲染流程表单数据
      showFlowData(){
        if(this.formBpm === true){
          let params = {id:this.formData.dataId};
          getAction(this.url.queryById,params).then((res)=>{
            if(res.success){
              this.edit (res.result);
            }
          });
        }
      },
      submitForm () {
        const that = this;
        // 触发表单验证
        this.form.validateFields((err, values) => {
          if (!err) {
            that.confirmLoading = true;
            let httpurl = '';
            let method = '';
            if(!this.model.id){
              httpurl+=this.url.add;
              method = 'post';
            }else{
              httpurl+=this.url.edit;
               method = 'put';
            }
            let formData = Object.assign(this.model, values);
            console.log("表单提交数据",formData)
            httpAction(httpurl,formData,method).then((res)=>{
              if(res.success){
                that.$message.success(res.message);
                that.$emit('ok');
              }else{
                that.$message.warning(res.message);
              }
            }).finally(() => {
              that.confirmLoading = false;
            })
          }
         
        })
      },
      popupCallback(row){
        this.form.setFieldsValue(pick(row,'createBy','createTime','updateBy','updateTime','sysOrgCode','projectName','projectNo','company','pipeline','station','projectStatus','construtionDate','startingDate','constructionUnit','projectManager','contactNumber','ip1','ip2','ip3','ip4'))
      },
    }
  }
</script>