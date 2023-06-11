<template>
	<view>
		<!-- 未登录 -->
		<template v-if="!loginStatus">
			<view class="flex align-center justify-center py-2 font">
				登录人大人，体验更多功能
			</view>
			<other-login></other-login>
			<view class="flex align-center justify-center py-2 font text-secondary" @click="openLogin">
				账号/手机登录 <text class="ml-1 iconfont icon-jinru"></text>
			</view>
		</template>

		<!-- 已登录 -->
		<view v-else class="flex align-center p-2" hover-class="bg-light">
			<image :src="avatar"
			style="width: 100rpx;height: 100rpx;"
			class="rounded-circle"></image>
			<view class="flex flex-column flex-1 px-2">
				<text class="font-lg font-weight-bold text-dark">{{user.username}}</text>
				<text class="font text-muted">
					总帖子{{myData[0].num}}</text>
			</view>
			<text class="iconfont icon-jinru"></text>
		</view>
		
		<view class="flex align-center px-3 py-2">
			<view class="flex-1 flex flex-column align-center justify-center"@click="openHistoryPost">
				<text class="font-lg font-weight-bold">{{myData[0].num}}</text>
				<text class="font text-muted">{{myData[0].name}}</text>
			</view>
			<view class="flex-1 flex flex-column align-center justify-center"@click="postToday">
				<text class="font-lg font-weight-bold">{{myData[1].num}}</text>
				<text class="font text-muted">{{myData[1].name}}</text>
			</view>
			<view class="flex-1 flex flex-column align-center justify-center"@click="friendsList">
				<text class="font-lg font-weight-bold">{{myData[2].num}}</text>
				<text class="font text-muted">{{myData[2].name}}</text>
			</view>
			<view class="flex-1 flex flex-column align-center justify-center"@click="followList">
				<text class="font-lg font-weight-bold">{{myData[3].num}}</text>
				<text class="font text-muted">{{myData[3].name}}</text>
			</view>
			<view class="flex-1 flex flex-column align-center justify-center"@click="fansList">
				<text class="font-lg font-weight-bold">{{myData[4].num}}</text>
				<text class="font text-muted">{{myData[4].name}}</text>
			</view>
		</view>
		
		<view class="px-3 py-2">
			<image src="/static/demo/RUC.jpg" mode="aspectFill"
			style="height: 250rpx;width: 100%;" class="rounded"></image>
		</view>
		
		<uni-list-item title="浏览历史" showExtraIcon @click="openHistory">
			<text slot="icon" class="iconfont icon-liulan"></text>
		</uni-list-item>
<!-- 		<uni-list-item title="社区认证" showExtraIcon>
			<text slot="icon" class="iconfont icon-huiyuanvip"></text>
		</uni-list-item> -->
		<uni-list-item title="审核帖子" showExtraIcon>
			<text slot="icon" class="iconfont icon-keyboard"></text>
		</uni-list-item>
		<!-- #ifdef MP -->
		<navigator url="../user-set/user-set" hover-class="none">
		<uni-list-item title="我的设置" showExtraIcon>
			<text slot="icon" class="iconfont icon-shezhi"></text>
		</uni-list-item>
		</navigator>
		<!-- #endif -->
		
		
	</view>
</template>

<script>
	import uniListItem from '@/components/uni-ui/uni-list-item/uni-list-item.vue';
	import otherLogin from '@/components/common/other-login.vue';
	import { mapState } from 'vuex'
	export default {
		components: {
			uniListItem,
			otherLogin
		},
		data() {
			return {
				myData:[{
					name:"帖子",
					num:0
				},{
					name:"动态",
					num:0
				},{
					name:"朋友",
					num:0
				},{
					name:"关注",
					num:0
				},{
					name:"粉丝",
					num:0
				}]
			}
		},
		onNavigationBarButtonTap() {
			uni.navigateTo({
				url: '../user-set/user-set'
			});
		},
		computed: {
			...mapState({
				loginStatus:state=>state.loginStatus,
				user:state=>state.user
			}),
			// 用户头像
			avatar(){
				return this.user.userpic ? this.user.userpic : '/static/interface.jpg'
			}
		},
		onShow() {
			if(this.loginStatus){
				this.getCounts()
			}
		},
		watch: {
			loginStatus(newValue, oldValue) {
				if(newValue){
					this.getCounts()
				} else {
					this.myData.forEach(item=>{
						item.num = 0
					})
				}
			}
		},
		methods: {
			// 获取用户相关数据
			getCounts(){

				this.$H.get('/user/getcounts/'+this.user.id,{},{
					token:true,
					notoast:true
				}).then(res=>{
					if(res){
						this.myData[0].num = res.post_count
						this.myData[1].num = res.today_posts_count
						this.myData[2].num = res.today_posts_count
						this.myData[3].num = res.withfollow_count
						this.myData[4].num = res.withfen_count
					}
				})
			},
			// 打开登录页
			openLogin(){
				uni.navigateTo({
					url: '../login/login',
				});
			},
			openHistory(){
				uni.navigateTo({
					url: '../history/history'
				});
			},
			openHistoryPost(){
				uni.navigateTo({
					url: '../history/historypost'
				});
			},
			postToday(){
				uni.navigateTo({
					url: '../history/postToday'
				});
			},
			friendsList(){
				uni.navigateTo({
					url: '../history/friendsList'
				});
			},
			followList(){
				uni.navigateTo({
					url: '../history/followList'
				});
			},
			fansList(){
				uni.navigateTo({
					url: '../history/fansList'
				});
			}
		}
	}
</script>

<style>

</style>
