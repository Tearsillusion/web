<template>
	<div class="web-team">
		<el-container>
		 
		  <el-main>
			  <el-container>
				<el-aside>
					<personalDetails></personalDetails>
				</el-aside>
				<el-main>
					<router-view />
				</el-main>
			  </el-container>
		  </el-main>
		<!--  <el-footer>
			  
		  </el-footer> -->
		</el-container>
	</div>
</template>

<script lang="ts">
	
	import { ref,provide,watch } from 'vue'
	import { useRouter } from 'vue-router'
	import personalDetails from './children/personalDetails.vue'
	export default {
		components:{
			personalDetails
		},
		setup(){
			const router = useRouter();
			const communityClick = ()=>{
				router.push('/community')
			}
			return {
				communityClick
			}
		}
		
	}
	
</script>

<style lang="scss">
	.web-team{
		width: 100%;
		height: 100%;
		display: flex;
		.el-container{
			
			.el-main{
				display: flex;
				padding: 0px;
				background-color: #f6f6f8;
				
				.el-container{
					.el-aside{
						width: 200PX;
						
					}
					.el-main{
						padding: 20px 20px 0 20px;
						box-sizing: border-box;
						height: 100%;
					}
				}
			}
			
		}
		
		
	}
</style>
