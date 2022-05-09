<template>
	<div class="user-manage">
		<div class="community-input">
			<div class="community-input-seach">
				<el-input
				      v-model="seachValue"
				      placeholder="请输入标题开始您的搜索"
					  @keyup.enter.native="seachClick"
				    />
				<img src="../../assets/image/icon_home_seach.png" @click="seachClick">
			</div>
		</div>
		<div class="user-manage-table" ref="totalHeight">
			 <el-table :data="tableData"
			 :height="tbodyHeight"
			  style="width: 100%">
			    <el-table-column  property="title" label="标题"  width="180">
			    </el-table-column>
				<el-table-column align="center" property="image_path" label="图片">
					<template #default="scope">
						<div>
							<img style="width: 60px;height: 60px;object-fit: cover;" :src="scope.row.image_path?scope.row.image_path:require('../../assets/image/icon-defalut.png')" >
						</div>
					</template>
				</el-table-column>
				<el-table-column align="center" property="content" label="内容" >
					<template #default="scope">
						<div v-html="scope.row.content" class="article-content"></div>
					</template>
				</el-table-column>
				<el-table-column align="center" property="review_status" label="审核状态" >
					<template #default="scope">
						<div :style="{color:scope.row.review_status==1?'':'red'}">{{scope.row.review_status==1?'已通过':'未通过'}}</div>
					</template>
				</el-table-column>
				<el-table-column align="center" property="review_dec" label="描述信息"></el-table-column>
				<el-table-column align="center" property="create_time" label="创建时间" >
				</el-table-column>
			    <el-table-column align="center" property="Operations" label="操作" width="180">
			      <template #default="scope">
					  <el-button size="small" @click="handleLook(scope.$index, scope.row)"
						>详情</el-button
					  >
			        <el-button size="small" @click="handleEdit(scope.$index, scope.row)"
			          >审核</el-button
			        >
			      </template>
			    </el-table-column>
			  </el-table>
		</div>
		<div class="user-page">
			<h1>共{{totalCount}}条 当前显示第{{currentPage}}页</h1>
			<div class="user-operation-button">
				<el-pagination background layout="prev, pager, next" :page-size="20" :total="totalCount" @current-change="currentChange">
				</el-pagination>
			</div>
		</div>
		<el-dialog v-model="dialogFormVisible" title="文章审核" width="500px" draggable>
		    <el-form>
		      <el-form-item label="选择审核状态">
		        <el-select v-model="userPermissions" placeholder="Please select a zone">
		          <el-option label="通过" :value="1" />
		          <el-option label="不通过" :value="0" />
		        </el-select>
		      </el-form-item>
			  <el-form-item label="输入描述信息">
			     <el-input v-model="review_dec"></el-input>
			  </el-form-item>
		    </el-form>
		    <template #footer>
		      <span class="dialog-footer">
		        <el-button @click="dialogFormVisible = false">取消</el-button>
		        <el-button type="primary" @click="submitClick"
		          >确定</el-button
		        >
		      </span>
		    </template>
		  </el-dialog>
	</div>
</template>

<script lang="ts" setup>
	import { ref,onMounted,nextTick } from 'vue' 
	import { ElMessage, ElMessageBox } from 'element-plus'
	import {HttpArticleManageList,HttpReviewArticle} from '../../public/http'
	import {
		useRouter
	} from 'vue-router'
	const router = useRouter();
	interface User {
	  id:number,
	  title:string,
	  image_path:string,
	  content:string,
	  create_time:string,
	  review_status:number,
	  review_dec:string,
	}
	let tableData = ref<User>([])
	let seachValue = ref("")
	let currentPage = ref(1)
	let totalPage = ref(0)
	let totalCount = ref(0)
	let dialogFormVisible = ref<Boolean>(false)
	let userPermissions = ref<number>(1)
	let userInfo = ref<any>("")
	let tbodyHeight = ref<any>(0)
	let review_dec = ref<any>("")
	const totalHeight = ref(null)
	// 确定修改
	const submitClick = ()=>{
		HttpReviewArticle({
			article_id:userInfo.value.id,
			review_type:userPermissions.value,
			review_dec:review_dec.value,
		}).then((res:any)=>{
			if(res.code === 200){
				ElMessage({
					message: res.message,
					type: 'success',
				})
				dialogFormVisible.value = false
				userMain(currentPage.value)
				return
			}
			ElMessage({
				message: res.message,
				type: 'warning',
			})
			
		})
	}
	// 查看文章详情
	const handleLook = (index: number, row: User) => {
		router.push({
			path: '/communityDetail',
			query: {
				id: row.id
			}
		})
	}
	// 修改弹框
	const handleEdit = (index: number, row: User) => {
	  userPermissions.value = row.review_status
	  dialogFormVisible.value = true
	  userInfo.value = row
	}
	// 文章管理列表
	const userMain = (page)=>{
		HttpArticleManageList(page,{
			key_word:seachValue.value
		}).then((res:any)=>{
			if(res.code === 200){
				tableData.value = res.data.acticleData
				currentPage.value = res.data.current_page
				totalPage.value = res.data.total_page
				totalCount.value = res.data.total_count
			}
		})
	}
	// 搜索点击
	const seachClick = ()=>{
		userMain(1)
	}
	// 监听页数改变
	const currentChange = (page)=>{
		userMain(page)
	}
	onMounted(async()=>{
		userMain(1)
		await nextTick()
		const tableHeight = totalHeight.value.offsetHeight
		tbodyHeight.value = tableHeight - 10
	})
	
</script>

<style lang="scss">
	.user-manage{
		width: 100%;
		height: 100%;
		.community-input{
			width: 100%;
			height: 50px;
			.community-input-seach{
				display: flex;
				justify-content: space-between;
				align-items: center;
				width: 30%;
				height: 35px;
				background-color: #ffffff;
				border-radius: 5px;
				padding: 0 10px;
			}
			input{
				width: calc(100% - 20px);
				background-color: transparent;
				border: 0;
				color: #1e1c2a;
				&::placeholder{
					color: #9195a1;
				}
			}
			img{
				width: 20px;
				height: 20px;
				cursor: pointer;
				&:hover{
					opacity: 0.6;
				}
			}
		}
		.user-manage-table{
			width: 100%;
			height: calc(100% - 50px - 80px);
			.article-content{
				overflow: hidden;
				display: -webkit-box;
				-webkit-box-orient: vertical;
				-webkit-line-clamp: 2;
			}
		}
		.user-page{
			width: 100%;
			height: 80px;
			display: flex;
			justify-content: space-between;
			padding: 0 20px;
			box-sizing: border-box;
			h1{
				color: #9195a1;
				font-size: 15px;
				letter-spacing: 2px;
				font-weight: 500;
			}
			.user-operation-button{
				display: flex;
				justify-content: flex-end;
				height: 50px;
				margin-top: 10px;
				button{
					background: white;
					border: 1px solid #ccc;
					color: var(--main-font-color);
					border-right: 0!important;
					font-size: 13px;
				}
				li{
					background: white;
					border: 1px solid #ccc;
					color: var(--main-font-color);
					font-size: 13px;
					border-radius: 0;
				}
				.el-pagination.is-background .el-pager li:not(.disabled).active{
					background: rgb(59, 115, 240);
					border: 1px solid rgb(59, 115, 240);
				}
				.el-pagination.is-background .btn-next, .el-pagination.is-background .btn-prev, .el-pagination.is-background .el-pager li{
					margin: 0;
					border-right: 0;
					width: 30px!important;
					height: 30px!important;
				}
				.el-pagination.is-background .btn-next:disabled, .el-pagination.is-background .btn-prev:disabled{
					background: white;
				}
				.btn-next{
					border-right: 1px solid #ccc!important;
				}
				
			}
		}
	}
</style>
