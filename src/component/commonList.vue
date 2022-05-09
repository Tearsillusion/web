<template>
	<el-card v-for="(item,index) in data" class="hoverClass common-list" :style="{marginRight:(index+1)%6 == 0?0:'20px'}">
		<img  :src="item.image_path?item.image_path:require('../assets/image/icon-defalut.png')" class="common-list-img"
			@error="(e)=>{e.target.src = require('../assets/image/icon-defalut.png')}" @click="communityClick(item)" />
		<div class="el-card-info">
			<div class="el-card-info-avatar">
				<span v-if="deleteShow" class="hoverActive" @click="deleteClick(item.id)">删除</span>
				<span v-if="editShow" class="hoverActive" @click="editClick(item.id)">编辑</span>
				<span v-if="publicShow" :style="{background:item.public_status?'':'red'}">{{item.public_status=='1'?'公开文章':'私密文章'}}</span>
			</div>
			<div class="el-card-review" :title="item.review_dec" v-if="!item.review_status&&reviewShow"><strong>审核不通过：{{item.review_dec}}</strong></div>
			<div class="el-card-title" @click="communityClick(item)"><strong>{{item.title}}</strong></div>
		</div>
		<div class="el-card-model" @click="communityClick(item)"></div>
	</el-card>
</template>

<script lang="ts" setup>
	import {
		ref
	} from 'vue'
	import {
		useRouter
	} from 'vue-router'
	const router = useRouter();
	const emits = defineEmits(['delete'])
	const props = defineProps({
		data: Array,
		deleteShow: Boolean,
		editShow: Boolean,
		publicShow: Boolean,
		reviewShow: Boolean,
	})
	const communityClick = (item) => {
		router.push({
			path: '/communityDetail',
			query: {
				id: item.id
			}
		})
	}
	const deleteClick = (id) => {
		emits("delete", id)
	}
	const editClick = (id) => {
		emits("edit", id)
	}
	
</script>

<style lang="scss">
	.common-list {
		width: calc(100% / 6 - 20px);
		height: 150px;
		cursor: pointer;
		display: inline-block;
		margin-bottom: 20px;
		position: relative;
		border: 0;
		border-radius: 15px;
		margin-right: 20px;
		.el-card-model{
			position: absolute;
			left: 0;
			top: 0;
			width: 100%;
			height: 100%;
			
		}
		.el-card__body {
			width: 100%;
			height: 100%;
			padding: 0;

			.common-list-img{
				position: absolute;
				left: 0;
				top: 0;
				width: 100%;
				height: 100%;
				object-fit: cover;
				
			}

			.el-card-info {
				padding: 10px;
				box-sizing: border-box;

				.el-card-info-avatar {
					display: flex;
					justify-content: space-between;
					align-items: center;
					span {
						width: 40px;
						height: 25px;
						background: rgba(0,0,0,0.2);
						color: white;
						text-align: center;
						line-height: 25px;
						border-radius: 20px;
						float: right;
						font-size: 12px;
						position: absolute;
						right: 5px;
						top: 5px;
						z-index: 2;
						&:nth-child(2){
							right: 60px;
						}
						&:nth-child(3){
							left:10px;
							top:0;
							height: 80px;
							width: 30px;
							border-radius: 25px;
							border-top-right-radius:0;
							border-top-left-radius:0;
							writing-mode:tb-rl;
							letter-spacing: 2px;
							line-height: 30px;
							background-color: rgba(59, 115, 240,0.3);
						}
					}
				}
				.el-card-title{
					
					position: absolute;
					left: 0;
					bottom: 0;
					z-index: 2;
					text-align: center;
					color: white;
					background: rgba(0,0,0,0.2);
					width: 100%;
					height: 30px;
					line-height: 30px;
					    
					strong{
						font-size: 13px;
						letter-spacing: 2px;
						font-weight: 500;
						text-align: center;
						width: 100%;
						display: block;
						padding: 0 10px;
						box-sizing: border-box;
						display: -webkit-box;
						-webkit-box-orient: vertical;
						-webkit-line-clamp: 1;
						overflow: hidden;
					}
				}
				.el-card-review{
					@extend .el-card-title;
					bottom: 30px;
					background-color: rgba(195,36,16,0.2);
				}
			}
		}
	}
</style>
