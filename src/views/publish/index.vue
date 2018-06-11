<template>
  <div class="upload" ref="uploader">
    <div class="content">
        <div class="up-head">
            <i class="iconfont">&#xe616;</i>发布新闻图片
        </div>
        <div class="up-form">
            <div class="group clearfix">
                <p>标题</p>
                <textarea id="title" placeholder="投稿须知：敏感政治内容，黄暴内容的帖子，将会被直接删除。站长不想被请去喝茶 (┬＿┬)" v-model="title"></textarea>
                <p class="hint"></p>
            </div>
            <div class="group clearfix">
                <p>内容</p>
                <textarea id="title" placeholder="投稿须知：敏感政治内容，黄暴内容的帖子，将会被直接删除。站长不想被请去喝茶 (┬＿┬)" v-model="content"></textarea>
                <p class="hint"></p>
            </div>
            <div class="group clearfix">
                <p>上传图片</p>
                <div class="upload_warp_img_div"> <uploader></uploader></div>
            </div>
            <ul class="tags">
              <li v-for="(item,index) in Tags "  v-on:click="changeTags($event,item)" class="detail-btn">{{item.name}}</li>           
            </ul>
            <div class="upload-bnt" v-on:click="upload()">发布</div>
        </div>

    </div>
    <div class="side">
        <h2>图片投稿</h2>
        <h3>1. 不准飚车,不准飚车,不准飚车</h3>
        <h3>2. 标题违规的帖子将会直接删除</h3>
        <h3>3. 拒绝有水印和非原创的帖子</h3>
        <h3>4. 上古时期的图就不要发了</h3>
        <h3>5. 禁止发布一些不可描述的东西</h3>
        <h3>6. 饺子韭菜馅的好吃</h3>
        <h3>7. 发表帖子即同意以上条款</h3>
    </div>
    
</div>
</template>

<style>
   @import '../../assets/css/page/upload.css';
   .upload_warp_img{padding-left:136px;padding-top:10px;overflow:hidden;}
   .upload_warp_img_div{
      height: 100px;
      width: 140px;
      float:left;
      margin-right:16px;
    float: left;
    border: 2px dashed #dddddd;
    height: 216px;
    width: 440px;
    position: relative;
   }
   .upload_warp_img_div_top{
     position:relative;
   }
   .upload_warp_img_div_del{
      position: absolute;
      top: 6px;
      width: 16px;
      right: 4px;
    }
   .upload_warp_img_div_text{
    position: absolute;
    top: 0;
    width: 100%;
    height: 30px;
    background-color: rgba(0, 0, 0, 0.4);
    line-height: 30px;
    text-align: left;
    color: #fff;
    font-size: 12px;
    text-indent: 4px;
    text-align:center;
   }
   .upload_img{
      max-width: 100%;
      max-height: 100%;
      vertical-align: middle;
    }
   .tags{
      overflow:hidden;
      margin-left:140px;
      margin-top:10px;
   }
   .detail-btn{
      display:inline-block;
      float:left;
      margin-right:16px;
      margin-bottom:10px;
      padding:0 10px;
      line-height:30px;
      border-radius:15px;
      border:3px dotted #dadada;    
   }
   .detail-selected{
      border:3px solid #41b883;  
   }

</style>

<script>
    import Alert from '../../components/Alert'
    import uploader from '../../components/uploader.vue';
    import api from '../../fetch/api.js';
    export default{
        components: {
            'uploader':uploader,
        },
        data(){
            return{
                isNowPage: true,
                title:'',
                content:'',
                Tags:[],
                ind:"",
                TagsData:[],
                TagObj:{}

            }
        },
        created() {
          if(!this.$store.state.userinfo.id){
            var _this=this;
            setTimeout(function(){
                            const url = 'login';
                            _this.$router.push({"path":url});
            },1000)
          }else{
            this.getTags();
          }
        },
        methods:{
          upload(){
            var imgData=this.$store.state.files;
            this.TagsData=[];
            for(var key in this.TagObj){
                 if(this.TagObj[key].active==1){
                     this.TagsData[this.TagsData.length]=this.TagObj[key].name;
                 }
            }
            alert(JSON.stringify(this.TagsData));
            var data={
                userid:this.$store.state.userinfo.id,
                title:this.title,
                content:this.content,
                imgData:imgData,
                tagsname:JSON.stringify(this.TagsData)
              }
              if(this.title == null ||this.title== undefined ||this.title==''){
                       alert('标题不能为空');
                       return;
              }
              if(this.content == null ||this.content== undefined ||this.content==''){
                       alert('内容不能为空');
                       return;
              }
              var that=this;
              api.PicUpload(data).then(function (response) {
                if(response.code=="200"){
                      Alert({
                          content:"发布成功"
                        })
                      that.$router.push({ path: '/' })
              }
        
              })
               .catch(function (error) {
                console.log(error);
              });
      
          },
        getTags:function(){
       
         var data={}
         var that=this;
                  api.GetTags(data).then(function (response) {
                    
                    if(response.code=="200"){
                     that.$store.state.Tags=response.data;
                     that.Tags=response.data;
                     for(var i=0;i<that.Tags.length;i++){
                        that.TagObj[that.Tags[i].name]=that.Tags[i];
                     }
                    }else{
                       Alert({
                          content: response.msg
                        })
                    }                
                  })
                 .catch(function (error) {
                  console.log(error);
                 });      
                  
         },
         changeTags:function(e,item){
              if (e.target.className.indexOf("detail-selected") == -1) {
                            e.target.className = "detail-btn detail-selected"; //切换按钮样式
                            this.TagObj[item.name].active=1;                            
                        } else {
                            e.target.className = "detail-btn";//切换按钮样式
                            this.TagObj[item.name].active=0;                           
                        }
            
         }
      }
    }
</script>
