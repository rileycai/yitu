<template>
  <div class="one">
    <div class="space">
      <router-link to="/">主 页</router-link>   
    </div>
    <div class="content">
      <div>
        <el-input placeholder="请选择行政区域" v-model="input" :disabled="true">
        </el-input>
      </div>
      <div>
        <el-tabs v-model="editableTabsValue" type="card" closable @tab-remove="removeTab" @tab-click="handleClick">
          <el-tab-pane  v-for="item in editableTabs" :key="item.name"  :label="item.title" :name="item.name" >
              <div id="data-show">
                <el-button type="text" v-for="subItem in item.content" @click="onChoose(subItem)" :key="subItem.code">{{subItem.name}}</el-button>
              </div>
          </el-tab-pane>
        </el-tabs>
      </div>
    </div>
    <div class="space">
      <router-link to="/two">题目二</router-link>    
    </div>
  </div>
</template>

<script>
export default {
  name: 'one',
  data () {
    return {
      input:'请选择行政区域',  //选择的行政区域
      editableTabsValue: '1',  //当前激活的Tab页
      editableTabs: [{         
          title: '请选择',
          name: '1',
          content:{}     //每个Tab页存储的内容
        }],
      tabIndex: 1      //一共有几个Tab页
    }
  },
  methods: {
    // 初始化，读JSON数据
    fetchData(){
      this.$http.get('../../static/pcas-code.json').then(res => {
        this.editableTabs[0].content=res.data;
        console.log(this.data);
        this.$message({
          message: '数据导入成功！',
          type: 'success'
        });
      })
    },
    // 点击每个地点事件,向下扩展一层Tab
    handleClick(tab, event) {
        let max=this.tabIndex;
        let min=this.editableTabsValue
        for(let i=max-1;i>=min;i--){
          this.editableTabs.pop();
        }
        this.tabIndex=this.editableTabsValue;
        this.editableTabsValue=this.editableTabsValue.toString();
        this.showChoose();
    },
    // 输入框显示选择的地点
    showChoose(){
      this.input="";
      let len=this.editableTabs.length;
      for(let i=0;i<len;i++){
        if(i===0)
          this.input+=this.editableTabs[i].title;
        else if(this.editableTabs[i].title!="请选择")
          this.input+='/'+this.editableTabs[i].title;
      }
    },
    // 选择Tab事件
    onChoose(item){
      let len=this.editableTabs.length;
      this.editableTabs[len-1].title=item.name;
      if(item.children){  
        this.addTab(item.children);
      }
      this.showChoose();
    },
    // 添加Tab事件
    addTab(childeren) {
        let newTabName = ++this.tabIndex + '';
        this.editableTabs.push({
          title: '请选择',
          name: newTabName,
          content:childeren
        });
        this.editableTabsValue = newTabName;
    },
    // 消除Tab事件
    removeTab(tab) {
      let len =this.editableTabs.length;
      for(let i=len;i>tab;i--){
          this.editableTabs.pop();
      }
      if(tab == 1){
          this.editableTabs[0].title="请选择";
          this.input="请选择行政区域";
          this.editableTabsValue=tab.toString();
          this.tabIndex=tab;
      }else{
          this.editableTabs.pop();
          this.editableTabsValue=(tab-1).toString();
          this.tabIndex=tab-1;
          this.showChoose();   
        }     
      }
   },
  created() {
    this.fetchData();
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.one{
  box-sizing: border-box;
  display:-webkit-flex;
  padding: 5% 0;
  width:  100vw;
  height: 100vh;
  margin: 0;

}
.space{
  background-color:blanchedalmond;
  flex-grow:1;
  text-align: center;
  position:relative;
}
.space>*{
  position: absolute;
  top:50%;
  left: 50%;
  margin-left: -24px;
  margin-top: -10px;
}
.content{
  padding: 2%;
  flex-grow:2;
  max-width: 800px;
  border:1px solid #DCDFE6;
}
.content div{
  margin: 20px;
}
.content .el-input{
  width: 60%;
}

#data-show>.el-button{
  margin: 0px 20px 10px 0px;
  color: #606266;
}
#data-show>.el-button:hover{
  color: #409EFF;
}

</style>
