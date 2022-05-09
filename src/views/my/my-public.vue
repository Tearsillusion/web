<template>
	<div class="my-public">
		<div class="my-public-nav" v-if="store.state.userInfoX.user_type!=1">
			<span @click="navClick(1)" :style="{color:navIndex === 1 ?'rgb(59, 115, 240)':'#9195a0'}">文章</span>
			<span @click="navClick(2)" :style="{color:navIndex === 2 ?'rgb(59, 115, 240)':'#9195a0'}">新闻</span>
		</div>
		<div class="my-public-bottom">
			<commonList :data="communityData" @delete="deleteRelease" @edit="editRelease" :reviewShow="true" :publicShow="true"  :deleteShow="true" :editShow="true"></commonList>
			<div v-if="communityData.length === 0" class="no-data">
				<img src="../../assets/image/noData.png" >
				<p>您还没有发布的{{navIndex===1?'文章':'新闻'}}，快去发布吧！</p>
			</div>
		</div>
	</div>
</template>

<script lang="ts" setup>
	import commonList from '../../component/commonList.vue'
	import {HttpMyRelease,HttpDeleteMyRelease} from '../../public/http'
	import {onMounted,ref,inject} from 'vue'
	import { ElMessage, ElMessageBox } from 'element-plus'
	import bus from '../../public/bus'
	const store = inject('store')
	const router = inject('router')
	let communityData = ref([])
	let navIndex = ref(1)
	
	// 文章切换
	const navClick = (val)=>{
		navIndex.value = val
		releaseMain(navIndex.value)
	}
	
	// 编辑发布
	const editRelease = (id)=>{
		router.push({
			path: '/communityUpdate',
			query: {
				id: id
			}
		})
	}
	// 发布列表
	const releaseMain = (type)=>{
		HttpMyRelease().then((res:any)=>{
			if(res.code === 200){
				communityData.value = []
				res.data.acticleDetail.forEach((res:any)=>{
					if(res.article_type === type){
						communityData.value.push(res)
					}
				})
			}
		})
	}
	// 删除发布
	const deleteRelease = (id)=>{
		ElMessageBox.confirm(
			'你确定要删除此发布嘛，一旦删除无法撤销',
			'警告',
			{
			  confirmButtonText: '确定',
			  cancelButtonText: '取消',
			  type: 'warning',
			}
		  ).then(() => {
			 HttpDeleteMyRelease(id).then((res:any)=>{
			 	if(res.code === 200){
			 		ElMessage({
			 		  message: `删除成功`,
			 		  type: 'success',
			 		})
			 		releaseMain(navIndex.value)
					bus.emit("reviseSuccess")
			 		return
			 	}
			 	ElMessage({
			 	  message: `删除失败`,
			 	  type: 'success',
			 	})
			 })
		})
		
		
	}
	onMounted(()=>{
		releaseMain(navIndex.value)
	})
	
</script>

<style lang="scss">
	.my-public{
		width: 100%;
		height: 100%;
		overflow: hidden;
		.my-public-nav{
			width: 100%;
			height: 30px;
			span{
				font-size: 15px;
				color: rgb(145, 149, 160);
				margin-right: 30px;
				cursor: pointer;
			}
		}
		.my-public-bottom{
			width: 100%;
			height: calc(100% - 30px);
			overflow: auto;
		}
	}
</style>
