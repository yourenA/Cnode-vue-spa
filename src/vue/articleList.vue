<template>
	<div>
		<nv-header></nv-header>
		<div class="artlist">
			<ul class="artlistTab clearfix">
				<li v-for="(item, index) in itemTab" :class="{'on':initIndex === index}" v-on:click="changeTab(index)">{{item.title}}</li>
			</ul>
			<div class="artlistCon">
				<router-link v-for="art in artlist" class="artitem clearfix" :to="{name:'article',params:{id:art.id}}">
					<router-link class="avatar" href="javascript:void(0);" :to="{name:'userhome',params:{username:art.author.loginname}}">
						<img :src="art.author.avatar_url" :alt="art.author.loginname">
					</router-link>
					<div class="art-inf">
						<div class="info-top">
							<em :title="art.tab | getArticleTab(art.good, art.top)"
								:class="art.good | getArticleClass(art.top)">
								{{art.tab | getArticleTab(art.good, art.top)}}
							</em>
							<a class="title">{{art.title}}</a>
						</div>
						<div class="info-bottom">
							<span class="rp-count">{{art.reply_count}}/{{art.visit_count}}</span>
							<span class="last-time">{{art.last_reply_at | getLastTime }}</span>
						</div>

					</div>
				</router-link>
			</div>
			<div class="loadingbox" v-show="showLoading">
				<div class="loading"></div>
			</div>
		</div>
		<nv-top></nv-top>
	</div>
</template>
<script>
	import axios from 'axios';
	//导入公共的组件
	import nvHeader from '../components/header.vue';
	import nvTop from '../components/returnTop.vue';
	export default {
		data : function() {
			return {
				initIndex : 0,
				scroll : true, //避免快速滚动，无限拉取数据源
				showLoading : false, //加载列表小icon
				itemTab : [
					{'title' : '全部', 'type' : 'all'},
					{'title' : '精华', 'type' : 'good'},
					{'title' : '分享', 'type' : 'share'},
					{'title' : '问答', 'type' : 'ask'},
					{'title' : '招聘', 'type' : 'job'}
				],
				artlist : [],
				searchKey : {
					'page' : 1,
					'limit' : 20, //每页加载20条
					'tab' : 'all' //分页 有all ask share job good
				}

			}
		},
		mounted : function() {
			this.gerArtlist(this.initIndex);
            window.addEventListener('scroll', this.scrollArtlist, false);
		},
		methods :  {
			// 标签tab切换方法
			changeTab : function(index) {
				this.initIndex = index;
				this.searchKey.tab = this.itemTab[index].type;
				this.artlist = [];
				this.searchKey.limit = 20;
				this.gerArtlist(this.initIndex);
			},
			// 获取数据方法
			gerArtlist : function() {
				let rqdata = this.searchKey;
				axios.get('https://cnodejs.org/api/v1/topics', {
					params : rqdata
				})
				.then((response) => {
					console.log(response)
					if (response.data.success) {
						this.artlist = response.data['data'];
						this.scroll = true;
						this.showLoading = false;
					}
				})
				.catch((error) => {
					console.log(error);
				})
			},
			// 超过滚动获取数据方法
			scrollArtlist : function() {
				if (this.scroll) {
					let totalheight = parseFloat(window.innerHeight) + parseFloat(window.scrollY);
	          		if (document.body.clientHeight <= totalheight + 200) {
	                    this.scroll = false;
	                    this.searchKey.limit += 20;
	                    this.showLoading = true;
	                    setTimeout(() => {
	                    	this.gerArtlist();
	                    }, 2000)
	                }
				}
			}
		},
		/**
		 * components向template传递组件
		 * */
		components : {
			nvHeader,
			nvTop
		},
		beforeDestroy : function() {
			// 退出组件解除window的scroll事件,防止别的页面下拉加载。
			window.removeEventListener('scroll', this.scrollArtlist, false);
		}
	}
</script>
<style lang="sass"  rel="stylesheet/scss">
	.artlist{
		overflow: hidden;
		margin: 0 5px;
		padding-top : 4rem;
		.artlistTab {
			margin: 0 auto 10px;
			width: 100%;
			height: 3rem;
			background: #F6F6F6;
			border-radius: 7px;
			li {
				float: left;
				width: 15%;
				height: 2rem;
				margin: 0.5rem 0 0 4%;
				color: #80BD01;
				text-align: center;
				line-height: 2rem;
			}
			.on {
				background: #80BD01;
				color: #fff;
				border-radius: 5px;
			}
		}
		.artlistCon {
			width: 100%;
			.artitem {
				position: relative;
				display: flex;
				padding: 10px;
				height: 3rem;
				background: #fff;
				border-bottom: 1px solid #eee;
				.avatar {
					display: inline-block;
					width: 3rem;
					height: 3rem;
					img {
						display: inline-block;
						width:  3rem;
						height:  3rem;
					}
				}
				.art-inf {
					padding-left: 0.5rem;
					flex: 1;
					width: 0;
					height:3rem;
                    .info-top{
                        height: 1.5rem;
                        line-height: 1.5rem;
                        display: flex;
                        em {
                            width: auto;
                            padding: 2px 4px;
                            background: #e5e5e5;
                            border-radius: 3px;
                            color: #999;
                            font-size: 0.8rem;
                        }
                        a {
                            padding-left: 5px;
                            flex: 1;
                            width: 0;
                            overflow: hidden;
                            color: #000;
                            font-size: 1rem;
                            white-space: nowrap;
                            text-overflow: ellipsis;
                        }
                        .put_good, .put_top {
                            background: #80bd01;
                            color: #fff;
                        }

                    }
                    .info-bottom{
                        height: 1.5rem;
                        line-height: 1.5rem;
                        display: flex;
                        justify-content:space-between;
                        vertical-align: bottom;
                        font-size: 0.8rem;
                        color: #444444;
                        .rp-count {
                        }
                        .last-time {
                        }

                    }

				}
			}

		}
		.loadingbox {
			position: relative;
			width: 100%;
			height: 40px;
			.loading {
				position: absolute;
				width: 25px;
				height: 25px;
				left: 0px;
				top: 0px;
				right: 0px;
				bottom: 0px;
				margin: auto;
				background: url('../img/loading.gif') no-repeat;
				background-size: 100%;
			}
		}
	}
</style>