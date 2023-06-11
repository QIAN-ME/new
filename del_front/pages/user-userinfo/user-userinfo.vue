<!-- <template>
	<view>
		<uni-list-item title="头像" @click="changeUserpic">
			<view class="flex align-center" slot="right">
				<image :src="user.userpic ? user.userpic : '/static/default.jpg'"
				style="width: 100rpx;height: 100rpx;"
				class="rounded-circle"></image>
				<text class="iconfont icon-bianji1 ml-2"></text>
			</view>
		</uni-list-item>
		<uni-list-item title="昵称">
			<view class="flex align-center" slot="right">
				<input class="uni-input text-right" v-model="username" />
				<text class="iconfont icon-bianji1 ml-2"></text>
			</view>
		</uni-list-item>
		<uni-list-item title="性别" @click="changeSex">
			<view class="flex align-center" slot="right">
				<text>{{sexText}}</text>
				<text class="iconfont icon-bianji1 ml-2"></text>
			</view>
		</uni-list-item>
		<picker mode="date" :value="birthday" @change="onDateChange">
			<uni-list-item title="生日">
				<view class="flex align-center" slot="right">
					<text>{{birthday}}</text>
					<text class="iconfont icon-bianji1 ml-2"></text>
				</view>
			</uni-list-item>
		</picker>
		<uni-list-item title="情感" @click="changeEmotion">
			<view class="flex align-center" slot="right">
				<text>{{emotionText}}</text>
				<text class="iconfont icon-bianji1 ml-2"></text>
			</view>
		</uni-list-item>
		<uni-list-item title="年级" @click="grade">
			<view class="flex align-center" slot="right">
				<text>{{gradetext}}</text>
				<text class="iconfont icon-bianji1 ml-2"></text>
			</view>
		</uni-list-item>
		<uni-list-item title="学院" @click="school">
			<view class="flex align-center" slot="right">
				<text>{{schooltext}}</text>
				<text class="iconfont icon-bianji1 ml-2"></text>
			</view>
		</uni-list-item>
		<uni-list-item title="专业">
			<view class="flex align-center" slot="right">
				<input class="uni-input text-right" v-model="satisfaction" />
				<text class="iconfont icon-bianji1 ml-2"></text>
			</view>
		</uni-list-item>
		<uni-list-item title="家乡" @click="showCityPicker">
			<view class="flex align-center" slot="right">
				<text>{{pickerText}}</text>
				<text class="iconfont icon-bianji1 ml-2"></text>
			</view>
		</uni-list-item>
		
		<view class="py-2 px-3">
			<button class="bg-main text-white" style="border-radius: 50rpx;border: 0;" type="primary" @click="submit">完成</button>
		</view>
		
		
		<mpvue-city-picker :themeColor="themeColor" ref="mpvueCityPicker" :pickerValueDefault="cityPickerValueDefault" @onConfirm="onConfirm"></mpvue-city-picker>
		
	</view>
</template>

<script>
	const sexArray = ['男', '女']
	const emotionArray = ['单身', '恋爱']
	import uniListItem from '@/components/uni-ui/uni-list-item/uni-list-item.vue';
	
	import mpvueCityPicker from '@/components/uni-ui/mpvue-citypicker/mpvueCityPicker.vue';
	
	import { mapState } from 'vuex'
	
	export default {
		components: {
			uniListItem,
			mpvueCityPicker
		},
		data() {
			return {
				themeColor: '#007AFF',
				cityPickerValueDefault: [0, 0, 1],
				pickerText: '',
				
				username:" ",
				sex:" ",
				emotion:" ",
				gradetext:" ",
				schooltext:" ",
				satisfaction:" ",
				birthday:" "
			}
		},
		// 监听返回
		onBackPress() {
		  if (this.$refs.mpvueCityPicker.showPicker) {
		  	this.$refs.mpvueCityPicker.pickerCancel();
		    return true;
		  }
		},
		// 监听页面卸载
		onUnload() {
			if (this.$refs.mpvueCityPicker.showPicker) {
				this.$refs.mpvueCityPicker.pickerCancel()
			}
		},
		onLoad() {
			let userinfo = this.user.userinfo
			if(userinfo){
				this.pickerText = userinfo.path
				this.username = this.user.username
				this.sex =  userinfo.sex
				this.emotion = userinfo.qg
				this.gradetext = userinfo.gradetext
				this.schooltext  = userinfo.schooltext
				this.birthday  = userinfo.birthday
			}
		},
		computed: {
			...mapState({
				user:state=>state.user
			}),
			sexText() {
				return sexArray[this.sex]
			},
			emotionText(){
				return emotionArray[this.emotion]
			}
		},
		methods: {
			showCityPicker(){
				this.$refs.mpvueCityPicker.show()
			},
			// 三级联动提交事件
			onConfirm(e) {
				this.pickerText = e.label
			},
			// 修改生日
			onDateChange(e){
				this.birthday = e.detail.value
			},
			// 修改头像
			changeUserpic(){
				uni.chooseImage({
					count:1,
					sizeType:["compressed"],
					sourceType:["album","camera"],
					success: (res) => {
						this.$H.upload('/edituserpic',{
							filePath: res.tempFilePaths[0],
							name: 'userpic',
							token:true
						}).then(result=>{
							console.log(result);
							this.$store.commit('editUserInfo',{
								key:"userpic",
								value:result.data
							})
							uni.showToast({
								title: '修改头像成功',
								icon: 'none'
							});
						}).catch(err=>{
							console.log(err);
						})
					}
				})
			},
			// 修改性别
			changeSex(){
				uni.showActionSheet({
				    itemList: sexArray,
				    success:(res)=>{
				        this.sex = res.tapIndex
				    }
				});
			},
			// 修改情感
			changeEmotion(){
				uni.showActionSheet({
				    itemList: emotionArray,
				    success:(res)=>{
				        this.emotion = res.tapIndex
				    }
				});
			},
			//修改年级
			grade(){
				let gradetextArray = ['大一','大二','大三','大四','研一','研二','研三','博']
				uni.showActionSheet({
				    itemList: gradetextArray,
				    success:(res)=>{
				        this.gradetext = gradetextArray[res.tapIndex]
				    }
				});
			},
			// 修改学院
			school(){
				let schooltextArray = ['经济学院','财政金融学院','法学院','马克思主义学院','社会与人口学院',' 国际关系学院','新闻学院','艺术学院','外国语学院',
								'环境学院','信息学院','商学院','公共管理学院','劳动人事学院','信息资源管理学院','统计学院']
				uni.showActionSheet({
				    itemList: schooltextArray,
				    success:(res)=>{
				        this.schooltext = schooltextArray[res.tapIndex]
				    }
				});
			},
			// 提交
			submit(){
				let obj = {
					name:this.username,
					sex:this.sex,
					qg:this.emotion,
					gradetext:this.gradetext,
					schooltext:this.schooltext,
					satisfaction:this.satisfaction,
					birthday:this.birthday,
					path:this.pickerText
				}
				this.$H.post('/edituserinfo',obj,{
					token:true
				}).then(res=>{
					console.log(res);
					this.$store.commit('editUserInfo',{
						key:"username",
						value:this.username
					})
					this.$store.commit('editUserUserInfo',obj)
					uni.navigateBack({
						delta: 1
					});
					uni.showToast({
						title: '修改资料成功',
						icon: 'none'
					});
				})
			}
		}
	}
</script>

<style>

</style>
 -->
 <template>
 	<view>
 		<uni-list-item title="头像" @click="changeUserpic">
 			<view class="flex align-center" slot="right">
 				<image :src="user.userpic ? user.userpic : '/static/default.jpg'"
 				style="width: 100rpx;height: 100rpx;"
 				class="rounded-circle"></image>
 				<text class="iconfont icon-bianji1 ml-2"></text>
 			</view>
 		</uni-list-item>
 		<uni-list-item title="昵称">
 			<view class="flex align-center" slot="right">
 				<input class="uni-input text-right" v-model="username" />
 				<text class="iconfont icon-bianji1 ml-2"></text>
 			</view>
 		</uni-list-item>
 		<uni-list-item title="性别" @click="changeSex">
 			<view class="flex align-center" slot="right">
 				<text>{{sexText}}</text>
 				<text class="iconfont icon-bianji1 ml-2"></text>
 			</view>
 		</uni-list-item>
 		<picker mode="date" :value="birthday" @change="onDateChange">
 			<uni-list-item title="生日">
 				<view class="flex align-center" slot="right">
 					<text>{{birthday}}</text>
 					<text class="iconfont icon-bianji1 ml-2"></text>
 				</view>
 			</uni-list-item>
 		</picker>
 		<uni-list-item title="情感" @click="changeEmotion">
 			<view class="flex align-center" slot="right">
 				<text>{{emotionText}}</text>
 				<text class="iconfont icon-bianji1 ml-2"></text>
 			</view>
 		</uni-list-item>
		<uni-list-item title="年级" @click="changegrade">
			<view class="flex align-center" slot="right">
				<text>{{gradeText}}</text>
				<text class="iconfont icon-bianji1 ml-2"></text>
			</view>
		</uni-list-item>
 		<uni-list-item title="学院" @click="changeJob">
 			<view class="flex align-center" slot="right">
 				<text>{{job}}</text>
 				<text class="iconfont icon-bianji1 ml-2"></text>
 			</view>
 		</uni-list-item>
 		<uni-list-item title="家乡" @click="showCityPicker">
 			<view class="flex align-center" slot="right">
 				<text>{{pickerText}}</text>
 				<text class="iconfont icon-bianji1 ml-2"></text>
 			</view>
 		</uni-list-item>
 		
 		<view class="py-2 px-3">
 			<button class="bg-main text-white" style="border-radius: 50rpx;border: 0;" type="primary" @click="submit">完成</button>
 		</view>
 		
 		
 		<mpvue-city-picker :themeColor="themeColor" ref="mpvueCityPicker" :pickerValueDefault="cityPickerValueDefault" @onConfirm="onConfirm"></mpvue-city-picker>
 		
 	</view>
 </template>
 
 <script>
 	const sexArray = ['保密','男', '女']
 	const emotionArray = ['保密','单身', '恋爱']
	const gradeArray = ['大一','大二','大三','大四','研一','研二','研三','博']
 	import uniListItem from '@/components/uni-ui/uni-list-item/uni-list-item.vue';
 	
 	import mpvueCityPicker from '@/components/uni-ui/mpvue-citypicker/mpvueCityPicker.vue';
 	
 	import { mapState } from 'vuex'
 	
 	export default {
 		components: {
 			uniListItem,
 			mpvueCityPicker
 		},
 		data() {
 			return {
 				themeColor: '#007AFF',
 				cityPickerValueDefault: [0, 0, 1],
 				pickerText: '',
 				
 				username:"",
 				sex:"",
 				emotion:"",
 				job:"",
 				birthday:"",
				grade:""
 			}
 		},
 		// 监听返回
 		onBackPress() {
 		  if (this.$refs.mpvueCityPicker.showPicker) {
 		  	this.$refs.mpvueCityPicker.pickerCancel();
 		    return true;
 		  }
 		},
 		// 监听页面卸载
 		onUnload() {
 			if (this.$refs.mpvueCityPicker.showPicker) {
 				this.$refs.mpvueCityPicker.pickerCancel()
 			}
 		},
 		onLoad() {
 			let userinfo = this.user.userinfo
 			if(userinfo){
 				this.pickerText = userinfo.path
 				this.username = this.user.username
 				this.sex =  userinfo.sex
 				this.emotion = userinfo.qg
 				this.job  = userinfo.job
 				this.birthday  = userinfo.birthday
				this.grade = userinfo.grade
 			}
 		},
 		computed: {
 			...mapState({
 				user:state=>state.user
 			}),
 			sexText() {
 				return sexArray[this.sex]
 			},
 			emotionText(){
 				return emotionArray[this.emotion]
 			},
			gradeText(){
				return gradeArray[this.grade]
			}
 		},
 		methods: {
 			showCityPicker(){
 				this.$refs.mpvueCityPicker.show()
 			},
 			// 三级联动提交事件
 			onConfirm(e) {
 				this.pickerText = e.label
 			},
 			// 修改生日
 			onDateChange(e){
 				this.birthday = e.detail.value
 			},
 			// 修改头像
 			changeUserpic(){
 				uni.chooseImage({
 					count:1,
 					sizeType:["compressed"],
 					sourceType:["album","camera"],
 					success: (res) => {
 						this.$H.upload('/edituserpic',{
 							filePath: res.tempFilePaths[0],
 							name: 'userpic',
 							token:true
 						}).then(result=>{
 							console.log(result);
 							this.$store.commit('editUserInfo',{
 								key:"userpic",
 								value:result.data
 							})
 							uni.showToast({
 								title: '修改头像成功',
 								icon: 'none'
 							});
 						}).catch(err=>{
 							console.log(err);
 						})
 					}
 				})
 			},
 			// 修改性别
 			changeSex(){
 				uni.showActionSheet({
 				    itemList: sexArray,
 				    success:(res)=>{
 				        this.sex = res.tapIndex
 				    }
 				});
 			},
 			// 修改情感
 			changeEmotion(){
 				uni.showActionSheet({
 				    itemList: emotionArray,
 				    success:(res)=>{
 				        this.emotion = res.tapIndex
 				    }
 				});
 			},
			changegrade(){
				uni.showActionSheet({
				    itemList: gradeArray,
				    success:(res)=>{
				        this.grade = res.tapIndex
				    }
				});
			},
 			// 修改职业
 			changeJob(){
 				let JobArray = ['经济学院','财政金融学院','法学院','马克思主义学院','社会与人口学院',' 国际关系学院','新闻学院','艺术学院','外国语学院',
								'环境学院','信息学院','商学院','公共管理学院','劳动人事学院','信息资源管理学院','统计学院']
 				uni.showActionSheet({
 				    itemList: JobArray,
 				    success:(res)=>{
 				        this.job = JobArray[res.tapIndex]
 				    }
 				});
 			},
 			// 提交
 			submit(){
 				let obj = {
 					name:this.username,
 					sex:this.sex,
 					qg:this.emotion,
 					job:this.job,
 					birthday:this.birthday,
 					path:this.pickerText
 				}
 				this.$H.post('/edituserinfo',obj,{
 					token:true
 				}).then(res=>{
 					console.log(res);
 					this.$store.commit('editUserInfo',{
 						key:"username",
 						value:this.username
 					})
 					this.$store.commit('editUserUserInfo',obj)
 					uni.navigateBack({
 						delta: 1
 					});
 					uni.showToast({
 						title: '修改资料成功',
 						icon: 'none'
 					});
 				})
 			}
 		}
 	}
 </script>
 
 <style>
 
 </style>
 