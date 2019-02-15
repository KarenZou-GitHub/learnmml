<template>
  <div v-if="now.type == 'node'" id="subNode">
    <el-menu
      :default-active="NodeactiveIndex"
      @select="handleSelect"
      mode="horizontal">
      <el-menu-item index="1">字段设置</el-menu-item>
      <el-menu-item index="2">参数设置</el-menu-item>
      <el-menu-item index="3">执行调优</el-menu-item>
    </el-menu>
    <!-- 设置名称 -->
    <div class="sub" v-show="NodeactiveIndex === '1'">
      <ml-fields-dialog :datafield="datafield1" :description="'转为DOUBLE类型'"></ml-fields-dialog>
      <ml-fields-dialog :datafield="datafield" :description="'转为INT类型'"></ml-fields-dialog>
      <ml-fields-dialog :datafield="datafieldTESTONE" :description="'我想把这个设成参数，由组件调用'"></ml-fields-dialog>
      <ml-label :title="'组件名称'" :value="nownode.defName"></ml-label>
      <el-form :model="nodeFrom" ref="nodeFrom">
        <el-form-item label="Task名称" prop="name">
          <el-input v-model="nodeFrom.name"></el-input>
        </el-form-item>
      </el-form>
    </div>
    <!-- 字段设置 -->
    <div class="sub" v-show="NodeactiveIndex === '2'">
      <ml-fields-dialog :datafield="datafield1" :description="'转为DOUBLE类型'"></ml-fields-dialog>
      <ml-fields-dialog :datafield="datafield" :description="'转为INT类型'"></ml-fields-dialog>
    </div>
    <!-- 参数设置 -->
    <div class="sub" v-show="NodeactiveIndex === '3'">
      <el-form :model="nodeFrom" ref="nodeFrom">
        <el-form-item label="Task名称" prop="name">
          <el-input v-model="nodeFrom.name"></el-input>
        </el-form-item>
      </el-form>
    </div>
    <!-- 执行调优 -->
  </div>
  <div v-else  :id="'subNode'">
    <el-menu
      id="subExperiment"
      :default-active="activeIndex"
      mode="horizontal">
    <el-menu-item index="1">实验属性</el-menu-item>
    </el-menu>
    <div class="sub">
      <ml-label :title="'所属项目'" :value="nowExperiment.projectName"></ml-label>
      <ml-label :title="'创建日期'" :value="nowExperiment.createdTime"></ml-label>
      <el-form :model="experimentFrom" ref="experimentFrom">
        <el-form-item label="实验名称" prop="name">
          <el-input v-model="experimentFrom.name"></el-input>
          <!-- <span>{{ experimentFrom.name }}</span> -->
        </el-form-item>
        <el-form-item label="描述" prop="description">
          <el-input type="textarea" v-model="experimentFrom.description"></el-input>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
  import api from '../../api/api'
  import MlFieldsDialog from "../fieldsdialog/FieldsDialog.vue";
  import MlItem from "../mlitem/MlItem.vue";
  import MlLabel from "../mllabel/MlLabel.vue";
  export default {
    components: {
      MlLabel,
      MlItem,
      MlFieldsDialog
    },
    props: ['now'],
    name: 'MlSubMain',
    data () {
      return {
        nowExperiment:{},
        nownode:{},
        activeIndex: '1',
        NodeactiveIndex:'1',
        experimentFrom:{
            name:'',
            description:''
        },
        nodeFrom:{
          name:''
        },
        datafieldTESTONE:[{
          id: "",
          label: '',
          children: [{
            id: "",
            label: ''}]
        },{
          id: "",
          label: '',
          children: [{
            id: "",
            label: ''}]
        }],
        datafield:[{
          id: "type_INT",
          label: 'INT',
          children: [{
            id: "int1",
            label: 'int1'},
            {
              id: "int2",
              label: 'int2'
            }, {
              id: "int3",
              label: 'int3'
            },{
              id: "int4",
              label: 'int4'
            },{
              id: "int5",
              label: 'int5'
            },{
              id: "int6",
              label: 'int6'
            }]
        }, {
          id: "type_STRING",
          label: 'STRING',
          children: [{
            id: "str7",
            label: 'str7'
          }, {
            id: "str8",
            label: 'str8'
          }]
        }],
        datafield1:[{
          id: "type_DOUBLE",
          label: 'DOUBLE',
          children: [{
            id: "double1",
            label: 'double1'},
            {
              id: "double2",
              label: 'double2'
            }, {
              id: "double3",
              label: 'double3'
            },{
              id: "double4",
              label: 'double4'
            },{
              id: "double5",
              label: 'double5'
            },{
              id: "double6",
              label: 'double6'
            }]
        }, {
          id: "type_STRING",
          label: 'STRING',
          children: [{
            id: "str7",
            label: 'str7'
          }, {
            id: "str8",
            label: 'str8'
          }]
        }],
        defaultProps: {
          children: 'children',
          label: 'label'
        }
      }
    },
    mounted: function () {
      var form = new FormData();
      form.append("ID", this.now.id);
      api.getExperiment(form).then((response) => response.json())
        .then((data) => {
          this.nowExperiment =data.data;
          this.experimentFrom.name = this.nowExperiment.name;
          this.experimentFrom.description = this.nowExperiment.description;
        });
    },
    watch:{
      now:{
        handler:function(val){
          var form = new FormData();
          form.append("ID", val.id);
          console.log('Infon >>'+val.id);
          console.log('Infon >>'+val.type);
          if(val.type == "node"){
            api.getNode(form).then((response) => response.json())
              .then((data) => {
                this.nownode =data.data;
                this.nodeFrom.name = this.nownode.name;
                //datafield在这里重新定义一下，来来来加油试一下
                var inPortsTESTONE = this.nownode.inPorts;
                var outPortsTESTONE = this.nownode.outPorts;
                this.datafieldTESTONE[0].label='inports';
                console.log('datafieldTESTONE=',this.datafieldTESTONE);
                //这里点击然后获取的时候是没有错的，主要是有异步，当时那个组件已经渲染过了，值也已经传过去了，所以没有办法这样子更新，果然还是需要用到store，不然是没有办法组件信息及时更新的
                this.datafieldTESTONE[0].children[0].id='inports1';
                this.datafieldTESTONE[0].children[0].label='inports1';
              });
          }else{
            api.getExperiment(form).then((response) => response.json())
              .then((data) => {
                this.nowExperiment =data.data;
                this.experimentFrom.name = this.nowExperiment.name;
                this.experimentFrom.description = this.nowExperiment.description;
              });
          }
        },
        deep:true//对象内部的属性监听，也叫深度监听
      },
    },
    methods: {
      handleSelect(key) {
        this.NodeactiveIndex = key;
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
#subNode .el-tree{
  max-height: 400px;
  border-right: 1px solid #c0c4cc;
  border-bottom: 1px solid #c0c4cc;
  border-left: 1px solid #c0c4cc;
}
#subNode .el-tree-node__content{
  display: block;
  padding: 2px 0;
  border-top: 1px solid #c0c4cc;
}
#subNode .el-tree-node__expand-icon{
  float: right;
}
#subNode .el-checkbox{
  padding-left: 6px;
}
#subNode .el-menu-item{
  width: 33.33%;
  text-align: center;
  padding: 0 8px;
  font-size: 12px;
  height: 40px;
  line-height: 40px;
}
#subNode #subExperiment li{
  width: 100%;
  text-align: center;
}
.sub{
  height: 100%;
  padding: 5px 10px 5px;
}
  #subNode input{
    font-size: 14px;
    line-height: 30px;
    height: 30px;
  }
  #subNode .el-form-item__content{
    line-height: 30px;
  }
</style>
