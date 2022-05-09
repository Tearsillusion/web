<template>
	<div class="user-manage">
		<div class="community-input">
			<div class="community-input-seach">
				<el-input
				      v-model="seachValue"
				      placeholder="请输入昵称开始您的搜索"
					  @keyup.enter.native="seachClick"
				    />
				<img src="../../assets/image/icon_home_seach.png" @click="seachClick">
			</div>
		</div>
		<div class="user-manage-table" ref="totalHeight">
			 <el-table :data="tableData"
			 :height="tbodyHeight"
			  style="width: 100%">
			    <el-table-column  property="nickName" label="昵称"  width="180">
			    </el-table-column>
				<el-table-column align="center" property="avatar" label="头像">
					<template #default="scope">
						<div>
							<img style="width: 60px;height: 60px;object-fit: cover;" :src="scope.row.avatar?scope.row.avatar:require('../../assets/image/icon-defalut.png')" >
						</div>
					</template>
				</el-table-column>
				<el-table-column align="center" property="account" label="账号" >
				</el-table-column>
				<el-table-column align="center" property="account" label="权限" >
					<template #default="scope">
						<div :style="{color:scope.row.user_type==2?'rgb(59, 115, 240)':scope.row.user_type==3?'red':''}">{{scope.row.user_type==1?'普通用户':scope.row.user_type==2?'管理员':'超级管理员'}}</div>
					</template>
				</el-table-column>
				<el-table-column align="center" property="create_time" label="创建时间" >
				</el-table-column>
			    <el-table-column align="center" property="Operations" label="操作">
			      <template #default="scope">
			        <el-button size="small" @click="handleEdit(scope.$index, scope.row)"
			          >修改权限</el-button
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
		<el-dialog v-model="dialogFormVisible" title="用户权限修改" width="500px" draggable>
		    <el-form>
		      <el-form-item label="选择修改权限类型">
		        <el-select v-model="userPermissions" placeholder="Please select a zone">
		          <el-option label="普通用户" :value="1" />
		          <el-option label="管理员" :value="2" />
				  <el-option label="超级管理员" :value="3" />
		        </el-select>
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
	import {HttpPersonalList,HttpReviseUserPermissions} from '../../public/http'
	interface User {
	  id:number,
	  nickName:string,
	  avatar:string,
	  account:string,
	  create_time:string,
	  user_type:number,
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
	const totalHeight = ref(null)
	// 确定修改
	const submitClick = ()=>{
		HttpReviseUserPermissions({
			user_id:userInfo.value.id,
			user_permissions:userPermissions.value,
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
	// 修改弹框
	const handleEdit = (index: number, row: User) => {
	  userPermissions.value = row.user_type
	  dialogFormVisible.value = true
	  userInfo.value = row
	}
	// 用户列表
	const userMain = (page)=>{
		HttpPersonalList(page,{
			key_word:seachValue.value
		}).then((res:any)=>{
			if(res.code === 200){
				tableData.value = res.data.userData
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
