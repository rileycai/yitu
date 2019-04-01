<template>
  <div class="two">
    <div class="img-content">
      <canvas id="myCanvas" ><span>亲，您的浏览器不支持canvas，换个浏览器试试吧！</span></canvas>
    </div>
    <div class="img-operator">
       <el-button type="primary" icon="el-icon-more" plain @click="drawDot()">点  </el-button>
       <el-button type="primary" icon="el-icon-minus" plain @click="drawLine()">线  </el-button>
       <el-button type="primary" icon="el-icon-circle-check-outline" plain @click="drawCircle()">圆  </el-button>
       <el-button type="primary" icon="el-icon-news" plain @click="drawRect()">矩形</el-button>  
       <el-button type="danger" icon="el-icon-delete" plain @click="clearDraw()">清除</el-button>
       <el-button type="warning" icon="el-icon-refresh" plain @click="eraser()">橡皮</el-button>
       <el-button type="info" icon="el-icon-picture" plain @click="downloadFile()">保存</el-button>
       <div class="block">
          <p>笔画粗细</p>
          <el-slider  v-model="lineWidth" :step="1" show-stops :max="8" :min="1">
          </el-slider>
       </div>
       <div class="block">
         <p>颜色选择</p>
         <el-color-picker v-model="color" show-alpha :predefine="predefineColors">
         </el-color-picker>
       </div>   
    </div>
    <div class="img-operator router">
      <router-link to="/">主 页</router-link>  
      <router-link to="/one">题目一</router-link>  
    </div>
    <div class="img-show">
      <el-upload action="https://jsonplaceholder.typicode.com/posts/" list-type="picture-card" :on-preview="handlePictureCardPreview"
        :on-remove="handleRemove">
        <i class="el-icon-plus"></i>
      </el-upload>
      <el-dialog :visible.sync="dialogVisible">
        <img width="100%" :src="dialogImageUrl" id="scream" alt="">
        <el-button type="primary" icon="el-icon-edit" circle @click="modifyImage"></el-button>
      </el-dialog>
    </div>
  
  </div>
</template>

<script>
export default {
  name: 'two',
  data () {
    return {
       dialogImageUrl: '',
       dialogVisible: false,
       drawing:false,
       canvas:{},
       ctx:{},
       lineWidth:2,
       color: '#F56C6C',
       predefineColors: [
          '#409EFF',
          '#67C23A',
          '#F56C6C',
          '#ff4500',
          '#ff8c00',
          '#ffd700',
          '#90ee90',
          '#00ced1',
          '#1e90ff',
          '#c71585'
       ]
    }
  },
  methods: {
    // 初始化canvas宽度
    fetchData(){
      this.canvas=document.getElementById("myCanvas");
      this.ctx=this.canvas.getContext("2d");
      this.ctx.canvas.width=this.ctx.canvas.clientWidth;
      this.ctx.canvas.height=this.ctx.canvas.clientHeight;
    },
    //清除图片
    clearDraw(){
      this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height);
    },
    //画线
    drawLine(){
      let canvas = document.getElementById("myCanvas");
      let ctx = canvas.getContext("2d");
      ctx.lineWidth = this.lineWidth;//笔触粗细
      ctx.strokeStyle = this.color;
      canvas.onmousedown = function(ev) {
          var  ev = ev || event;
          ctx.beginPath();
			    ctx.moveTo(ev.clientX - canvas.offsetLeft,ev.clientY - canvas.offsetTop);
			    document.onmousemove = function(ev) {
            var ev = ev || event;
				    ctx.lineTo(ev.clientX - canvas.offsetLeft,ev.clientY - canvas.offsetTop);
				    ctx.stroke();
			    }
			    document.onmouseup = function(ev) {
				    document.onmousemove = document.onmousedown = null;
				    ctx.closePath();
			    }
      }
    },
    //画矩形
    drawRect(){
      let canvas = document.getElementById("myCanvas");
      let ctx = canvas.getContext("2d");
      ctx.lineWidth = this.lineWidth;//笔触粗细
      ctx.strokeStyle = this.color;
      canvas.onmousedown = function(ev) {
          var  ev = ev || event;
          var disX=ev.clientX-canvas.offsetLeft;
          var disY=ev.clientY-canvas.offsetTop;
			    document.onmouseup = function(ev) {
            if(ev.clientX<=ctx.canvas.width && ev.clientY<=ctx.canvas.height){
                document.onmousedown = null;
                ctx.strokeRect(disX,disY, ev.clientX-disX, ev.clientY-disY);
            }
			    }
      }
    },
    //画圆
    drawCircle(){
      let canvas = document.getElementById("myCanvas");
      let ctx = canvas.getContext("2d");
      ctx.lineWidth = this.lineWidth;//笔触粗细
      ctx.strokeStyle = this.color;
      canvas.onmousedown = function(ev) {
          var  ev = ev || event;
          var disX=ev.clientX-canvas.offsetLeft;
          var disY=ev.clientY-canvas.offsetTop;
			    document.onmouseup = function(ev) {
            if(ev.clientX<=ctx.canvas.width && ev.clientY<=ctx.canvas.height){
                ctx.beginPath();
                let radius=parseInt(Math.sqrt((ev.clientX-disX)*(ev.clientX-disX)+(ev.clientY-disY)*(ev.clientY-disY))/2);
                ctx.arc(parseInt((ev.clientX+disX)/2), parseInt((ev.clientY+disY)/2), radius, 0,  2*Math.PI, true);
                ctx.stroke();
                document.onmousedown = null;
            }
			    }
      }
    },
    //画点
    drawDot(){
      let canvas = document.getElementById("myCanvas");
      let ctx = canvas.getContext("2d");
      ctx.lineWidth = this.lineWidth;//笔触粗细
      ctx.fillStyle= this.color;
      canvas.onmousedown = function(ev) {
          var  ev = ev || event;
          var disX=ev.clientX-canvas.offsetLeft;
          var disY=ev.clientY-canvas.offsetTop;
          
			    document.onmouseup = function(ev) {
              let radius=ctx.lineWidth*2;
              ctx.beginPath();
              ctx.arc(disX, disY,radius, 0,  2*Math.PI, true);
              ctx.fill();
              document.onmousedown = null;
			    }
      }

    },
    eraser() {
      let canvas = document.getElementById("myCanvas");
      let ctx = canvas.getContext("2d");
      var radius=this.lineWidth*2;
		  canvas.onmousedown = function(e) { 
          ctx.save();
		      ctx.beginPath();
		      ctx.arc(e.clientX-canvas.offsetLeft, e.clientY-canvas.offsetTop, radius, 0, Math.PI * 2, false);
		      ctx.clip();
		      ctx.clearRect(0, 0, canvas.width, canvas.height);
		      ctx.restore();
			    var drawing = true;
			    document.onmousemove = function(e) {
				    if (drawing) {
					    ctx.save();
		          ctx.beginPath();
		          ctx.arc(e.clientX-canvas.offsetLeft, e.clientY-canvas.offsetTop, radius, 0, Math.PI * 2, false);
		          ctx.clip();
		          ctx.clearRect(0, 0, canvas.width, canvas.height);
		          ctx.restore();
				    }
			    }
			  document.onmouseup = function() {
				  drawing = false;
			  }
		  }
	 },
    //下载文件
    downloadFile(){
      let canvas = document.getElementById("myCanvas");
      let save_link = document.createElementNS('http://www.w3.org/1999/xhtml', 'a');
      save_link.href = canvas.toDataURL("image/png");
      save_link.download = 'picture.png';
      let event = document.createEvent('MouseEvents');
      event.initMouseEvent('click', true, false, window, 0, 0, 0, 0, 0, false, false, false, false, 0, null);
      save_link.dispatchEvent(event);
      this.$message({
          message: '文件下载成功！',
          type: 'success'
      });
    },
    //删除图片
    handleRemove(file, fileList) {
        this.$message({
          message: '图片删除成功！',
          type: 'success'
        });
    },
    //预览图片
    handlePictureCardPreview(file) {
        // console.log(file);
        this.dialogImageUrl = file.url;
        this.dialogVisible=true;
    },
    //编辑图片
    modifyImage(){
        this.clearDraw();
        let img=document.getElementById("scream");
        let offsetX=parseInt((this.ctx.canvas.width-img.width)/2);
        let offsetY=parseInt((this.ctx.canvas.height-img.height)/2);
        this.ctx.drawImage(img,offsetX>10?offsetX:10,offsetY>10?offsetY:10);
        this.dialogVisible=false;
    }
  },
  mounted() {
    this.fetchData();
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.two{
  box-sizing: border-box;
  display: grid;
  grid-template-columns: 6fr 1.5fr 0.5fr;
  grid-template-rows: 3fr 1fr;
  padding: 0;
  width:  100vw;
  height: 100vh;
  margin: 0;

}
.img-content{
  background-color:#F2F6FC;
  position: relative;

}
.img-content canvas{
  position: absolute; 
  width: 75vw;
  height: 75vh;
  left: 0;
  right: 0;
  /* background-color: aquamarine; */
}
.img-operator{
  background-color:#EBEEF5;

}
.router{
  background-color:#E4E7ED;
}
.img-operator>.el-button{
  margin: 20px 10px 0 10px;

}
.img-operator>a{
  display: block;
  margin: 20px;
}
.img-show{
  grid-column: 1 / 4;
  justify-self: start;
  align-self: center;
  padding-left: 20px;
 
}
.block{
  margin:20px 10px;
}

</style>
