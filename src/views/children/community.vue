<template>
	<div class="community">
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
		<div class="community-list" >
			<el-container>
				<el-main @scroll="handleScroll">
					<commonRectList :data="communityData"></commonRectList>
				</el-main>
				<el-aside>
					<div class="community-list-title">
						<span>新闻/公告</span>
					</div>
					<div class="community-list-bottom" @scroll="handleNewScroll">
						<commonNewList :data="newData"></commonNewList>
						<div v-if="newData.length === 0" class="no-data">
							<img src="../../assets/image/noData.png" >
							<p>暂无公告</p>
						</div>
					</div>
				</el-aside>
			</el-container>
		</div>
	</div>
</template>

<script lang="ts" setup>
	import {ref,onMounted} from 'vue'
	import commonRectList from '../../component/commonRectList.vue'
	import commonNewList from '../../component/commonNewList.vue'
	import {HttpArticleList,HttpNewList} from '../../public/http'
	import { ElMessage, ElMessageBox } from 'element-plus'
	
	let communityData = ref([])
	let currentPage = ref(1)
	let totalPage = ref(0)
	let totalCount = ref(0)
	let seachValue = ref("")
	let isPage = ref(1)
	// 新闻
	let newData = ref([])
	let currentNewPage = ref(1)
	let isNewPage = ref(1)
	
	
	
	// 监听文章是否到底部
	const handleScroll = (e)=>{
		const {scrollTop,clientHeight,scrollHeight} = e.currentTarget
		console.log(scrollTop + clientHeight,scrollHeight)
		if(scrollTop + clientHeight >= (currentPage.value==1?scrollHeight -1:scrollHeight) && isPage){
			isPage.value = 0
			currentPage.value++
			communityMain(currentPage.value,true)
		}
	}
	// 监听新闻是否到底部
	const handleNewScroll = (e)=>{
		const {scrollTop,clientHeight,scrollHeight} = e.currentTarget
		if(scrollTop + clientHeight >= scrollHeight && isNewPage){
			isNewPage.value = 0
			currentNewPage.value++
			newMain(currentNewPage.value,true)
		}
	}
	// 文章列表
	const communityMain = (page,type)=>{
		HttpArticleList(page,{
			key_word:seachValue.value
		}).then((res:any)=>{
			if(res.code === 200){
				if(type){
					communityData.value = communityData.value.concat(res.data.acticleData)
				}else{
					communityData.value = res.data.acticleData
				}
				currentPage.value = parseInt(res.data.current_page)
				totalPage.value = res.data.total_page
				totalCount.value = res.data.total_count
			}
			if(res.data.acticleData&&res.data.acticleData.length == 0){
				currentPage.value--
			}
			setTimeout(()=>{
				isPage.value = 1 
			})
			
		})
	}
	// 新闻列表
	const newMain = (page,type)=>{
		HttpNewList(page,{
			key_word:""
		}).then((res:any)=>{
			if(res.code === 200){
				if(type){
					newData.value = newData.value.concat(res.data.newData)
				}else{
					newData.value = res.data.newData
				}
				currentNewPage.value = parseInt(res.data.current_page)
			}
			if(res.data.newData&&res.data.newData.length == 0){
				currentNewPage.value--
			}
			setTimeout(()=>{
				isNewPage.value = 1 
			})
			
		})
	}
	// 搜索点击
	const seachClick = ()=>{
		communityMain(1)
	}
	// 分页点击
	const currentChange = (page)=>{
		communityMain(page)
	}
	
	onMounted(()=>{
		communityMain(1)
		newMain(1)
	})
	
</script>

<style lang="scss">
	.community{
		width: 100%;
		height: 100%;
		overflow: hidden;
		
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
		.community-list{
			width: 100%;
			height: calc(100% - 50px);
			.el-container{
				width: 100%;
				height: 100%;
				.el-main{
					display: block;
					padding:0!important;
				}
				.el-aside{
					width: 300px!important;
					background:white;
					padding: 12px;
					box-sizing: border-box;
					border-radius: 5px;
					.community-list-title{
						font-size: 15px;
						color: rgb(145, 149, 160);
						margin-right: 30px;
						cursor: pointer;
						height: 30px;
					}
					.community-list-bottom{
						width: 100%;
						height: calc(100% - 30px);
						overflow: auto;
					}
				}
			} 
		}
		.community-page{
			width: 100%;
			height: 60px;
			background: white;
			box-shadow: 0 0 10px 0 #eee;
			display: flex;
			justify-content: space-around;
			align-items: center;
			position: relative;
			.community-page-left{
				flex: 0.3;
				display: flex;
				justify-content: flex-start;
				position: absolute;
				left: 0;
				span{
					margin-right: 30px;
					font-size: 16px;
					letter-spacing: 1px;
				}
			}
			.community-page-right{
				flex: 1;
				display: flex;
				justify-content: center;
				
			}
		}
	}
</style>
