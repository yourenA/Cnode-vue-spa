<template>
	<div>
		<!--transition name=fade定义css中的fade-->
		<transition name="fade" class="router">
			<router-view class="r"></router-view>
		</transition>
	</div>
</template>
<style lang="sass">
	/*自动嗅探目标元素是否有 CSS 过渡或动画，并在合适时添加/删除 CSS 类名。*/
	/* 在 enter-active leave-active 中定义transition 与 终点状态*/
	.fade-enter-active, .fade-leave-active {
		transition: all .3s ease;
		transform: translateX(0px);
	}

	/*在 enter leave-active 中定义开始时的状态*/
	.fade-enter, .fade-leave-active {
		transform: translateX(-100%);
		opacity: 0;
	}
	.router {
		width: 100%;
		/*transform: translateX(-300px);*/
		/*position: absolute;*/
		/*transition: all .9s cubiz-bezier(.55, 0, .1, 1);*/
	}
	.r {
		width: 100%;
		position: absolute;
	}
</style>