<template>
	<view class="content">
		<!-- 状态栏 -->
		<view v-if="list.length !== 0" class="todo-header">
			<view class="todo-header_left">
				<text class="active-text">{{text}}</text>
				<text>{{listData.length}}</text>
			</view>
			<view class="todo-header_right">
				<view class="todo-header_right-item" :class="{'active-tab':activeIndex === 0}" @click="tab(0)">全部</view>
				<view class="todo-header_right-item" :class="{'active-tab':activeIndex === 1}" @click="tab(1)">代办</view>
				<view class="todo-header_right-item" :class="{'active-tab':activeIndex === 2}" @click="tab(2)">完成</view>
			</view>
		</view>
		<!-- 没有数据 -->
		<view v-if="list.length === 0" class="default">
			<view class="image-default">
				<image src="../../static/nocontent.png" mode="aspectFit"></image>
			</view>
			<view class="default-info">
				<view class="default-info_text">
					您还没有创办任何待办事项
				</view>
				<view class="default-info_text">
					点击下方+创建
				</view>
			</view>
		</view>
		<!-- 创办事项内容 -->
		<view v-else class="todo-content">
			<view 
				class="todo-list"
				v-for="(item, index) in listData" 
				:key="index" 
				@click="finish(item.id)"
				:class="{'todo-finish': item.checked}"
			>
				<view class="todo-list_checkbox">
					<view class="checkbox"></view>
				</view>
				<view class="todo-list_content">
					{{item.content}}
				</view>
			</view>
		</view>
		<!-- 创建按钮 -->
		<view class="create-todo" @click="create">
			<text class="iconfont iconadd" :class="{'create-todo-active':textShow}"></text>
		</view>
		<!-- 输入框 -->
		<view v-if="active" class="create-content" :class="{'create-show':textShow}">
			<view class="create-content-box">
				<view class="create-input">
					<input type="text" v-model="value" placeholder="请输入要创建的备忘录" />
				</view>
				<!-- 发布按钮 -->
				<view @click="inFoContent" class="create-button">
					创建
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				list: [],
				active: false,
				value: "",
				activeIndex: 0,
				text: "全部",
				textShow: false
			}
		},
		onLoad() {

		},
		computed:{
			listData () {
				let list  = JSON.parse(JSON.stringify(this.list))
				let newList = []
				//点击全部
				if (this.activeIndex === 0) {
					this.text = "全部"
					return list
				}
				//点击待办事项
				if (this.activeIndex === 1) {
					//checked = false
					list.forEach((item) => {
						if (!item.checked) {
							newList.push(item)
						}
					})
					this.text = "代办"
					return newList
				}
				//点击已完成
				if (this.activeIndex === 2) {
					list.forEach((item) => {
					if (item.checked) {
						newList.push(item)
					}
					})
					this.text = "完成"
					return newList
				}
			}
		},
		methods: {
			//打开输入框
			create () {
				this.active ? this.close() : this.open()
			},
			open () {
				this.active = true
				this.$nextTick(() => {
					setTimeout(() => {
						this.textShow = true
					},30)
				})
			},
			close () {
				this.textShow = false
				this.$nextTick(() => {
					setTimeout(() => {
						this.active = false
					},350)
				})
			},
			inFoContent () {
				if (this.value === '') {
					uni.showToast({
						title:"请输入内容！",
						icon:"none"
					})
					return
				}
				this.list.unshift({
					id:"id"+new Date().getTime(),
					content: this.value,
					checked: false
				})
				this.value = "";
				this.close()
			},
			finish (id) {
				let index = this.list.findIndex((item) => item.id === id )
				//当finish事件触发时，创建事项时所存储的id时间戳等于点击具体事项时的id，利用findIndex函数获取此条件成立时数据在list中的索引位置
				this.list[index].checked = !this.list[index].checked;
			},
			tab (index) {
				this.activeIndex = index
				
			}
		}
	}
</script>

<style>
	@import url("../../common/icon.css");
	.todo-header{
		position: fixed;
		top: 0;
		left: 0;
		display: flex;
		align-items: center;
		padding: 0 15px;
		font-size: 12px;
		color: #333333;
		box-sizing: border-box;
		width: 100%;
		height: 45px;
		box-shadow: -1px 1px 5px 0 rgba(0, 0, 0, 0.1);
		background: #FFFFFF;
		z-index: 8;
	}
	
	.todo-header_left{
		width: 100%;
	}
	
	.active-text{
		font-size: 14px;
		color: #279abf;
		padding-right: 10px;
	}
	
	.todo-header_right{
		display: flex;
		flex-shrink: 0;
	}
	
		
	.todo-header_right-item{
		padding: 0 15px 0 0;
	}
	
	.active-tab{
		color: #279abf;
	}
	
	.todo-content{
		position: relative;
		padding-top: 50px;
		padding-bottom: 100px;
	}
	
	.todo-list{
		position: relative;
		display: flex;
		flex-direction: row;
		padding: 15px;
		margin: 15px;
		box-shadow: -1px 1px 5px 1px rgba(0, 0, 0, .1), -1px 2px 1px 0 rgba(255, 255, 255) inset;
		border-radius: 10px;
		background: #cfebfd;
		color: #0c3864;
		font-size: 14px;
		overflow: hidden;
	}
	
	.todo-finish .checkbox{
		position: relative;
		background: #eee;
	}
	
	.todo-finish .checkbox:after{
		content: "";
		position: absolute;
		width: 10px;
		height: 10px;
		left: 50%;
		margin-left: -5px;
		top: 50%;
		margin-top: -5px;
		border-radius: 50%;
		background: #CFEBFD;
		box-shadow: 0px 0px 2px 0 rgba(0, 0, 0, .2) inset;
	}
	
	.todo-list:after{
		position: absolute;
		content: "";
		top: 0;
		left: 0;
		bottom: 0;
		width: 5px;
		background: #91d1e8;
	}
	
	.todo-list_checkbox{
		padding-right: 15px;
	}
	
	.checkbox{
		width: 20px;
		height: 20px;
		border-radius: 50%;
		background: #FFFFFF;
		box-shadow: 0 0 5px 1px rgba(0, 0, 0, .1);
	}
	
	.todo-finish .todo-list_content{
		color: #999999;
	}
	
	.todo-finish.todo-list:before{
		content: "";
		position: absolute;
		top: 0;
		bottom: 0;
		left: 40px;
		right: 10px;
		height: 2px;
		margin: auto 0;
		background: #bdcdd8;
	}
	
	.todo-finish.todo-list:after{
		background: #cccccc;
	}
	
	.create-todo{
		position: fixed;
		display: flex;
		justify-content: center;
		align-items: center;
		bottom: 20px;
		left: 0;
		right: 0;
		margin: 0 auto;
		width: 50px;
		height: 50px;
		border-radius: 50%;
		background-color: #deeff5;
		box-shadow: -1px 1px 5px 2px rgba(0, 0, 0, .1),-1px 1px 1px 0 rgba(255, 255, 255) inset;
	}
	
	.iconadd{
		font-size: 36px;
		color: #add8e6;
	}
	
	.create-content{
		position: fixed;
		bottom: 95px;
		left: 20px;
		right: 20px;
		transition: all 0.5s;
		opacity: 0;
		transform: scale(0) translateY(200%);
	}
	
	.create-show{
		opacity: 1;
		transform: scale(1) translateY(0);
	}
	
	.create-content-box{
		display: flex;
		align-items: center;
		padding: 0 0 0 15px;
		border-radius: 50px;
		background: #DEEFF5;
		box-shadow: -1px 1px 5px 2px rgba(0, 0, 0, .1);
		z-index: 2;		
	}
	
	.create-content-box:after{
		content: "";
		position: absolute;
		right: 0;
		left: 0;
		bottom: -9px;
		margin: 0 auto;
		width: 20px;
		height: 20px;
		background: #DEEFF5;
		transform: rotate(45deg);
	}
	
	.create-input{
		width: 100%;
		padding-left: 15px;
		color: #ADD8E6;
	}
	
	.create-button{
		display: flex;
		align-items: center;
		justify-content: center;
		flex-shrink: 0;
		width: 80px;
		height: 50px;
		border-radius: 50px;
		font-size: 16px;
		color: #88d4ec;
		box-shadow: -2px 0 2px 1px rgba(0, 0, 0, 0.1);
	}
	
	.create-content:after{
		content: "";
		position: absolute;
		right: 0;
		left: 0;
		bottom: -9px;
		margin: 0 auto;
		width: 20px;
		height: 20px;
		background: #DEEFF5;
		transform: rotate(45deg);
		box-shadow: 1px 1px 5px 2px rgba(0, 0, 0, .1);
		z-index: -1;
	}
	
	.default{
		padding-top: 80px;
	}
	
	.image-default{
		display: flex;
		justify-content: center;
	}
	
	.image-default image{
		width: 100%;
	}
	
	.default-info{
		text-align: center;
		font-size: 16px;
		color: #CCCCCC;
	}
	
	.iconadd{
		transition: transform 0.5s;
	}
	
	.create-todo-active{
		transform: rotate(135deg);
	}
</style>
