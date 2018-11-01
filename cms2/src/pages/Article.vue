<template>
	<div class="article">
		<!-- 按钮区 -->
		<div class="btns">
			<el-select size='mini' v-model="params.categoryId" clearable placeholder="所有栏目" style="width:120px">
		    <el-option v-for='c in categories' :key='c.id' :value='c.id' :label='c.name'> </el-option>
		  </el-select>
		  <el-input
		  	style='width:120px'
			  placeholder="请输入关键字" size='mini' 
			  v-model="params.keywords"
			  clearable>
			</el-input>
			<el-button size='mini' @click='toAddArticle'>新增</el-button>
			<el-button size='mini'>批量删除</el-button>
		</div>
		<!-- 按钮区结束 -->
		<!-- 列表区 -->
		<div class="article_tbl" v-loading='loading'>
			<el-table :data="articles" style="width: 100%" size='mini' :border='true'
			@selection-change="handleSelectionChange">
	     	<el-table-column
		      type="selection"
		      width="40">
		    </el-table-column>
		    <el-table-column
	        prop="id"
	        label="编号"
	        width="60">
	      </el-table-column>
	      <el-table-column
	        prop="title"
	        label="文章标题"
	        width="200">
	      </el-table-column>
	      <el-table-column
	        prop="category.name" 
	        label="所属栏目"
	        width="120">
	      </el-table-column>
	      <el-table-column
	        prop="author"
	        label="作者"
	        width="120">
	      </el-table-column>
	      <el-table-column
	        prop="publishtime"
	        label="发布时间">
	      </el-table-column>
	      <el-table-column
	        prop="readtimes"
	        label="阅读次数"
	        width="120">
	      </el-table-column>
	      <el-table-column label="操作" width='100'>
	      	<template slot-scope='{row}'>
	      		<i class="fa fa-trash" @click='deleteArticle(row.id)'></i>
	      		<i class="fa fa-pencil" @click='toUpdateArticle(row)'></i>
	      	</template>
	      </el-table-column>
	    </el-table>
		</div>
		<!-- 列表区结束 -->
		<!-- 分页 -->
		<div class="page">
			<el-pagination
		    layout="prev, pager, next"
		    :page-size='params.pageSize'
		    @current-change='handleCurrentChange'
		    :total="total">
		  </el-pagination>
		</div>
		<!-- 模态框 -->
		<el-dialog fullscreen :title="articleDialog.title" :visible.sync="articleDialog.visible">
			<!-- {{articleDialog.form}} -->
		  <el-form :model="articleDialog.form" size="mini" label-position="left" >
		    <el-form-item label="资讯标题" label-width="6em">
		      <el-input v-model="articleDialog.form.title" autocomplete="off"></el-input>
		    </el-form-item>
		    <el-form-item label="资讯栏目" label-width="6em">
		      <el-select v-model="articleDialog.form.categoryId" placeholder="一级栏目">
		        <el-option :key='c.id' v-for='c in categories' :label="c.name" :value="c.id"></el-option>
		      </el-select>
		    </el-form-item>
		    <el-form-item label="列表样式" label-width="6em">
		      <ul class="list_style">
		      	<li class="style_one" :class="{current:articleDialog.form.liststyle=='style-one'}" @click="articleDialog.form.liststyle = 'style-one'">
		      		<img src="@/assets/style_one.jpg" alt="">
		      	</li>
		      	<li class="style_two" :class="{current:articleDialog.form.liststyle=='style-two'}"  @click="articleDialog.form.liststyle = 'style-two'">
		      		<img src="@/assets/style_two.jpg" alt="">
		      	</li>
		      </ul>
		    </el-form-item>
		    <el-form-item label="缩略图" label-width="6em">

<el-upload
  action="http://120.78.164.247:8099/manager/file/upload"
  :on-success='handleUploadSuccess'
  list-type="picture">
  <el-button size="small" type="primary">点击上传</el-button>
  <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过500kb</div>
</el-upload>

		    </el-form-item>
		    <el-form-item label="资讯正文" label-width="6em">
		      <el-input v-model="articleDialog.form.content" type="textarea" :rows="6"></el-input>
		    </el-form-item>
		  </el-form>
		  <div slot="footer" class="dialog-footer">
		    <el-button size='mini' @click='closeArticleDialog'>取 消</el-button>
		    <el-button type="primary" size='mini' @click='saveOrUpdateArticle'>确 定</el-button>
		  </div>
		</el-dialog>
		<!-- 模态框结束 -->
	</div>
</template>
<script>
	import axios from '@/http/axios';
	export default {
		data(){
			return {
				loading:false,
				categories:[],
				articles:[],
				multipleSelection:[],
				total:10,
				params:{
					page:0,
					pageSize:10,
				},
				articleDialog:{
					title:'',	
					visible:false,
					form:{
						liststyle:'style-one',
						fileIds:[]
					}
				}
			}
		},
		watch:{
			params:{
				handler:function(){
					this.findAllArticles();
				},
				deep:true
			}
		},
		created(){
			this.findAllCategories();
			this.findAllArticles();
		},
		methods:{
			handleUploadSuccess(response, file, fileList){
				this.articleDialog.form.fileIds.push(response.data.id);
			},
			toAddArticle(){
				this.articleDialog.form = {
						liststyle:'style-one',
						fileIds:[]
					};
				this.articleDialog.title = '发布资讯';
				this.articleDialog.visible = true;
			},
			saveOrUpdateArticle(){
				axios.post('/manager/article/saveOrUpdateArticle',this.articleDialog.form)
				.then(()=>{
					this.$notify.success({
	          title: '成功',
	          message: '提交成功！'
	        });
	        this.closeArticleDialog();
	        this.findAllArticles();
				})
				.catch(()=>{
					this.$notify.error({
	          title: '错误',
	          message: '提交失败！'
	        });
				});
			},
			//关闭文章模态框
			closeArticleDialog(){
				this.articleDialog.visible = false;
			},
			handleCurrentChange(page){
				this.params.page = page -1;
			},
			/* 查询所有栏目 */
			findAllCategories(){
				axios.get('/manager/category/findAllCategory')
				.then(({data:result})=>{
					this.categories = result.data;
				})
				.catch(()=>{
					this.$notify.error({
	          title: '错误',
	          message: '网络异常！'
	        });
				})
				
			},
			/* 查询所有文章 */
			findAllArticles(){
				this.loading=true;
				axios.get('/manager/article/findArticle',{
					params:this.params
				})
				.then(({data:result})=>{
					this.total = result.data.total;
					this.articles = result.data.list;
				})
				.catch(()=>{
					this.$notify.error({
	          title: '错误',
	          message: '网络异常！'
	        });
				})
				.finally(()=>{
					this.loading=false;
				})
			},
			handleSelectionChange(val){
				this.multipleSelection = val;
			},
			deleteArticle(id){

			},
			toUpdateArticle(row){
				//1. 删除category,添加categoryId
				let article = _.clone(row);
				article.categoryId = article.category.id;
				delete article.category;

				article.fileIds = article.articleFileVMs.map(item=>item.cmsFile.id)
				delete article.articleFileVMs;

				for(let key in article){
					let val = article[key];
					if(!val){
						delete article[key];
					}
				}


				this.articleDialog.form = article;



				this.articleDialog.title = '修改资讯';
				this.articleDialog.visible = true;
			}
		}
	}
</script>
<style>
	.btns {
		margin-bottom: .5em;
	}
	i.fa {
		margin: 0 .3em;
		cursor: pointer;
	}
	i.fa.fa-trash {
		color: #F56C6C;
	}
	.list_style {

	}
	
	.list_style >li {
		width: 200px;
		height: 80px;
		border: 1px solid #ededed;
		border-radius: 3px;
		padding: .5em;
	}
	.list_style >li.current {
		border-color: #409eff;
	}
	.list_style >li img {
		width: 100%;
	}
	.list_style >li.style_one {
		float: left;
	}
	.list_style >li.style_two {
		margin-left: 220px;
	}
</style>






