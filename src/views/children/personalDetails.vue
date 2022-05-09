<template>
	<div class="personal-details">
		<div class="personal-details-photo">
			<el-avatar  @click="linkClick('set')" :size="60" :src="data.userInfo.avatar?data.userInfo.avatar:require('../../assets/image/defalut.png')" @error="(e)=>{e.target.src = require('../../assets/image/defalut.png')}" />
			<h2  @click="linkClick('set')">{{data.userInfo.nickName}}</h2>
			<div @click="manageClick(store.state.userInfoX.user_type)"  class="personal-details-photo-manage" v-if="store.state.userInfoX.user_type > 1">
				<img class="personal-details-photo-manage" :src="store.state.userInfoX.user_type==2?require('../../assets/image/icon_manage.png'):require('../../assets/image/icon_sup_manage.png') " >
				<span :style="{color:store.state.userInfoX.user_type==2?'#1d65ff':'#d81e06'}">{{store.state.userInfoX.user_type==2?'管理员':'超级管理员'}}</span>
			</div>
			
		</div>
		<div class="personal-details-content">
			<div v-for="(item,index) in data.navData" v-show="item.type==1?(store.state.userInfoX.user_type>1?true:false):true">
				<h3 v-if="item.menu_name">{{item.menu_name}}</h3>
				<div v-if="item.type==1?(store.state.userInfoX.user_type>1?true:false):true" class="personal-details-content-list" @click="linkClick(item.path,index)" >
					<span :style="{background:data.selectIndex === index?'#3b73f0':'#f4f4f5'}"><img :src="data.selectIndex === index?item.selectImg:item.img" ></span>
					<span :style="{color:data.selectIndex === index?'#1e1c2a':'#9195a0'}">{{item.name}} {{item.count_show?'('+item.count+')':''}}</span>
				</div>
			</div>
		</div>
		<!-- <div class="personal-details-public">
			<div title="发布作品" class="personal-details-go-public hoverActive" @click="linkClick('communityPublic')">
				+
			</div>
		</div> -->
	</div>
</template>

<script lang="ts" setup>
	import { reactive,onMounted,onUnmounted } from 'vue'
	import { ElMessage, ElMessageBox } from 'element-plus'
	import { inject } from "vue";
	import {HttpPersonal} from '../../public/http'
	import bus from '../../public/bus'
	const store = inject('store')
	const router = inject('router')
	

	let data = reactive({
		userInfo:{} as any,
		navData:[{
			name:'首页',
			path:'community',
			img:require('../../assets/image/icon_nav_home.png'),
			selectImg:require('../../assets/image/icon_nav_home_hover.png'),
			menu_name:"菜单",
		},{
			name:'我的发布',
			path:'public',
			count:0,
			img:require('../../assets/image/icon_nav_public.png'),
			selectImg:require('../../assets/image/icon_nav_public_hover.png'),
			count_show:true
		},{
			name:'我的评论',
			path:'news',
			count:0,
			img:require('../../assets/image/icon_nav_commonts.png'),
			selectImg:require('../../assets/image/icon_nav_commonts_hover.png'),
			count_show:true
		},{
			name:'我的收藏',
			path:'star',
			count:0,
			img:require('../../assets/image/icon_nav_star.png'),
			selectImg:require('../../assets/image/icon_nav_star_hover.png'),
			count_show:true
		},{
			name:'发布文章',
			path:'communityPublic',
			count:0,
			img:require('../../assets/image/icon_public.png'),
			selectImg:require('../../assets/image/icon_public_hover.png'),
		},{
			name:'用户管理',
			path:'userManage',
			img:require('../../assets/image/icon_user.png'),
			selectImg:require('../../assets/image/icon_user_hover.png'),
			type:1,
			menu_name:"管理",
		},{
			name:'文章审核',
			path:'articleReview',
			img:require('../../assets/image/icon_review.png'),
			selectImg:require('../../assets/image/icon_review_hover.png'),
			type:1
		},{
			name:'修改密码',
			path:'revise',
			img:require('../../assets/image/icon_nav_password.png'),
			selectImg:require('../../assets/image/icon_nav_password_hover.png'),
			menu_name:"设置",
		},{
			name:'退出登录',
			path:'',
			img:require('../../assets/image/icon_nav_logout.png'),
			selectImg:require('../../assets/image/icon_nav_logout_hover.png'),
		}],
		
		selectIndex:0,
		setIndex:-1
	})
	const manageClick = (type)=>{
		let message = ""
		if(type == 2){
			message = "管理员权限：1.审核文章，删除评论内容，2.修改普通用户账号权限"
		}else{
			message = "超级管理员权限：1.审核文章，删除评论内容，2.修改普通用户，管理员，超级管理员账号权限"
		}
		ElMessage({
		    showClose: true,
		    message:message,
		    type: 'success',
			duration:10000
		  })
	}
	// 跳转
	const linkClick = (path,index,type)=>{
		data.selectIndex = index
		sessionStorage.setItem("navSelect",index)
		if(path){
			router.push(`/${path}`)
			return
		}
		ElMessageBox.confirm(
			'你确定要退出吗?',
			'警告',
			{
			  confirmButtonText: '确定',
			  cancelButtonText: '取消',
			  type: 'warning',
			}
		  ).then(() => {
			store.commit("logoutMain",true)
		})
		
	}
	// 基本信息
	const personalMain = ()=>{
		HttpPersonal().then((res)=>{
			if(res.code === 200){
				data.userInfo = res.data
				data.navData[1].count = res.data.releaseCount
				data.navData[2].count = res.data.commentsCount
				data.navData[3].count = res.data.collectionCount
				let oldUserInfo = store.state.userInfoX
				oldUserInfo.nickName = res.data.nickName
				store.commit("userInfoMain",JSON.stringify(oldUserInfo))
			}
			console.log(res)
		})
	}
	 
	bus.on("reviseSuccess",personalMain)
	onUnmounted(()=>{
		bus.on("reviseSuccess",personalMain)
	})
	onMounted(()=>{
		personalMain()
		
		const navSelect = sessionStorage.getItem("navSelect")
		if(navSelect==null){
			return
		}
		data.selectIndex = Number(navSelect)
		console.log(data.selectIndex)
	})
</script>

<style lang="scss">
	.personal-details{
		position: relative;
		width: 100%;
		height: 100%;
		background-color: #ffffff;
		&-photo{
			width: 100%;
			display: flex;
			justify-content: center;
			align-items: center;
			flex-direction: column;
			padding: 20px;
			box-sizing: border-box;
			cursor: pointer;
			position: relative;
			h2{
				width: 90%;
				font-size: 15px;
				overflow: hidden;
				text-overflow: ellipsis;
				white-space: nowrap;
				text-align: center;
				color: #1e1c2a;
				letter-spacing: 1px;
				font-weight: bold;
			}
			.personal-details-photo-manage{
				width: 100%;
				display: flex;
				justify-content: center;
				align-items: center;
				img{
					width: 15px;
					height: 15px;
					margin-right: 5px;
				}
				span{
					font-size: 13px;
					color: #1d65ff;
					
				}
			}
		}
		.personal-details-content{
			padding: 0 20px;
			h3{
				color: #9195a1;
				font-size: 16px;
				letter-spacing: 1px;
				margin-bottom: 15px;
				font-weight: 500;
				&:nth-of-type(2){
					margin-top: 30px;
				}
			}
			.personal-details-content-list{
				width: 100%;
				display: flex;
				justify-content: flex-start;
				flex-direction: row;
				align-items: center;
				margin-bottom: 20px;
				cursor: pointer;
				&:hover{
					opacity: 0.8;
				}
				span{
					
					&:nth-child(1){
						width: 30px;
						height: 30px;
						background: #2e2e35;
						border-radius: 10px;
						display: flex;
						justify-content: center;
						align-items: center;
						img{
							width: 15px;
							height: 15px;
							vertical-align: -2px;
						}
					}
					&:nth-child(2){
						font-size: 14px;
						color: #9195a1;
						letter-spacing: 1px;
						margin-left: 10px;
					}
				}
				
			}
		}
		&-public{
			display: flex;
			justify-content: space-around;
			.personal-details-public-list{
				display: flex;
				flex-direction: column;
				text-align: center;
				cursor: pointer;
				strong{
					font-size: 18px;
				}
				span{
					font-size: 12px;
				}
			}
			.personal-details-go-public{
				position: fixed;
				bottom: 100px;
				right: 50px;
				width: 60px;
				height: 60px;
				box-shadow: 0 0 20px 0 #aaa;
				text-align: center;
				line-height: 60px;
				font-size: 35px;
				border-radius: 60%;
				z-index: 9;
				color: #1e1c2a;
				background-color: white;
			}
		}
		&-customer-service{
			display: flex;
			justify-content: center;
			flex-wrap: wrap;
			margin-top: 100px;
			img{
				width: 50%;
			}
		}
		&-logout{
			width: 100%;
			position: absolute;
			bottom: 0;
			button{
				width: 100%;
				height: 50px;
				border: 0;
				border-bottom: 1px solid #ccc;
				color: #222;
				border-radius: 0;
				background-color: transparent;
				margin: 0!important;
				text-align: center;
				
			}
		}
	}
	
</style>
