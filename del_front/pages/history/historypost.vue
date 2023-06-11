<template>
	<view>
		<common-list v-for="(item,index) in list" :key="index"
		:item="item" :index="index"></common-list>
		<divider v-if="list.length > 1"></divider>
		<load-more :loadmore="loadmore" v-if="list.length >1"></load-more>
		
		<no-thing v-if="list.length === 0"></no-thing>
	</view>
</template>

<script>
	import commonList from '@/components/common/common-list.vue';
	import noThing from '@/components/common/no-thing.vue';
	import loadMore from '@/components/common/load-more.vue';
	import {mapState} from 'vuex'
	
	export default {
		components: {
			commonList,
			loadMore,
		},
		data() {
			return {
				post: {
					list: [],
					loadmore: "上拉加载更多",
					page: 1,
				}
			}
		},
		computed: {
			...mapState({
				user:state=>state.user
			}),
			list() {
				return this.post.list
			},
			loadmore() {
				return this.post.loadmore
			}
		},
		onLoad() {
			this.getList()
			
			// 监听关注和顶踩操作
			uni.$on('updateFollowOrSupport',(e)=>{
				switch (e.type){
					case 'follow': // 关注
					this.follow(e.data.user_id)
						break;
					case 'support': // 顶踩
					this.doSupport(e.data)
						break;
				}
			})
			// 监听评论数变化
			uni.$on('updateCommentsCount',({user_id,count})=>{
				this.list.forEach((item)=>{
					if(item.id === id){
						item.comment_count = count
					}
				})
			})		
		},
		onUnload() {
			uni.$off('updateFollowOrSupport',(e)=>{})
			uni.$off('updateCommentsCount',(e)=>{})
		},
		methods: {
			// 关注
			follow(user_id){
				// 找到当前作者的所有列表
				this.post.list.forEach((item)=>{
					if(item.user_id === user_id){
						item.isFollow = true
					}
				})
				uni.showToast({ title: '关注成功' })
			},
			// 顶踩操作
			doSupport(e){
				// 拿到当前的选项卡对应的list
				this.post.list.forEach(item=>{
					if(item.id === e.id){
						// 之前没有操作过
						if (item.support.type === '') {
							item.support[e.type+'_count']++
						} else if (item.support.type ==='support' && e.type === 'unsupport') {
							// 顶 - 1
							item.support.support_count--;
							// 踩 + 1
							item.support.unsupport_count++;
						} else if(item.support.type ==='unsupport' && e.type === 'support'){ 					// 之前踩了
							// 顶 + 1
							item.support.support_count++;
							// 踩 - 1
							item.support.unsupport_count--;
						}
						item.support.type = e.type
					}
				})
				let msg = e.type === 'support' ? '顶' : '踩'
				uni.showToast({ title: msg + '成功' });
			},
			getList(){
				let page = this.post.page
				let isrefresh = page === 1
				this.$H.get('/user/'+this.user.id+'/post/'+page)
				.then(res=>{
					let list = res.list.map(v=>{
						return this.$U.formatCommonList(v)
					})
					this.post.list = isrefresh ? [...list] : [...this.post.list,...list],
					this.post.loadmore = list.length < 10 ? '没有更多了' : '上拉加载更多'
				}).catch(err=>{
					if(!isrefresh){
						this.page--
					}
				})
			}
		}
	}
</script>

<style>
</style>