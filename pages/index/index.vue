<template>
	<view class="">
		<view class="content">
			<view class="player-videodisc">
				<image src="../../static/record.png" class=""></image>
				<image :src="audioData[0].view_image" class="rotateImg" :style="styleObj"></image>
			</view>
			<view class="player-box">
				<view class="player-slider">
					<view class="player-currentTime">
						{{audioCurTime[0]}}:{{audioCurTime[1]}}
					</view>
					<slider class="slider" min="0" :max="duration" :value="currentTime" activeColor="#ccc" backgroundColor="#eee"
					 block-size="16" @change="changeProgress" />
					<view class="player-duration">
						<!-- 音频总时长用的是后端的数据，如果用innerAudioContext对象的duration在切换歌曲的时候会出现计算错误的情况 -->
						{{longth}}
						<!-- {{audioDuration[0]}}:{{audioDuration[1]}} -->
					</view>
				</view>
				<view class="play-bar">
					<view class="" @click='pre'>
						<text class="cuIcon-backwardfill" style="color: #ccc;"></text>
					</view>
					<view class="play-menu">
						<text class="cuIcon-playfill" style="color: #ccc;" :class="isPlay?'cuIcon-stop':'cuIcon-playfill'" @tap="playMusic"></text>
					</view>
					<view class="" @click='next'>
						<text class="cuIcon-play_forward_fill" style="color: #ccc;"></text>
					</view>
				</view>
			</view>
		</view>
		<view class="play-list">
			<view class="" style="position: relative;">
				播放列表
				<view class="text-underline">
				</view>
			</view>
		</view>
		
		<view>
			<view class="uni-padding-wrap uni-common-mt">
				<uni-segmented-control :current="current" :values="items" style-type="text" active-color="#007aff" @clickItem="onClickItem" />
			</view>
		</view>
		
		<view class="play-list-content" v-for="(item,index) in audioData" @tap="changeAudio(item)">
			<view class="play-list-content-title">
				{{item.name}}
			</view>
		</view>
	</view>
</template>

<script>
	
	const audioContext = uni.createInnerAudioContext();
	audioContext.autoplay = false;
	export default {
		data() {
			return {
				items: ['其他', '原创', '无损', '翻唱'],
				classMusicList: {"其他":[{"name":"All of me.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E5%85%B6%E4%BB%96/All%20of%20me.mp3","longth":""},{"name":"Another day of sun-陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E5%85%B6%E4%BB%96/Another%20day%20of%20sun-%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"lonely Christmas - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E5%85%B6%E4%BB%96/lonely%20Christmas%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"shape of you - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E5%85%B6%E4%BB%96/shape%20of%20you%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"乌兰巴托的一整夜.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E5%85%B6%E4%BB%96/%E4%B9%8C%E5%85%B0%E5%B7%B4%E6%89%98%E7%9A%84%E4%B8%80%E6%95%B4%E5%A4%9C.mp3","longth":""},{"name":"但愿人长久 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E5%85%B6%E4%BB%96/%E4%BD%86%E6%84%BF%E4%BA%BA%E9%95%BF%E4%B9%85%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"俩俩相忘 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E5%85%B6%E4%BB%96/%E4%BF%A9%E4%BF%A9%E7%9B%B8%E5%BF%98%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"匆匆 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E5%85%B6%E4%BB%96/%E5%8C%86%E5%8C%86%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"可能否.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E5%85%B6%E4%BB%96/%E5%8F%AF%E8%83%BD%E5%90%A6.mp3","longth":""},{"name":"夕阳之歌-陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E5%85%B6%E4%BB%96/%E5%A4%95%E9%98%B3%E4%B9%8B%E6%AD%8C-%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"外面的世界 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E5%85%B6%E4%BB%96/%E5%A4%96%E9%9D%A2%E7%9A%84%E4%B8%96%E7%95%8C%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"夜机-陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E5%85%B6%E4%BB%96/%E5%A4%9C%E6%9C%BA-%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"太阳-陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E5%85%B6%E4%BB%96/%E5%A4%AA%E9%98%B3-%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"小半 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E5%85%B6%E4%BB%96/%E5%B0%8F%E5%8D%8A%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"小橘猫-不染.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E5%85%B6%E4%BB%96/%E5%B0%8F%E6%A9%98%E7%8C%AB-%E4%B8%8D%E6%9F%93.mp3","longth":""},{"name":"小橘猫-女人花.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E5%85%B6%E4%BB%96/%E5%B0%8F%E6%A9%98%E7%8C%AB-%E5%A5%B3%E4%BA%BA%E8%8A%B1.mp3","longth":""},{"name":"小橘猫-完美孤独.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E5%85%B6%E4%BB%96/%E5%B0%8F%E6%A9%98%E7%8C%AB-%E5%AE%8C%E7%BE%8E%E5%AD%A4%E7%8B%AC.mp3","longth":""},{"name":"小橘猫-水星记.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E5%85%B6%E4%BB%96/%E5%B0%8F%E6%A9%98%E7%8C%AB-%E6%B0%B4%E6%98%9F%E8%AE%B0.mp3","longth":""},{"name":"小橘猫-白山茶.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E5%85%B6%E4%BB%96/%E5%B0%8F%E6%A9%98%E7%8C%AB-%E7%99%BD%E5%B1%B1%E8%8C%B6.mp3","longth":""},{"name":"往后余生 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E5%85%B6%E4%BB%96/%E5%BE%80%E5%90%8E%E4%BD%99%E7%94%9F%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"心动-陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E5%85%B6%E4%BB%96/%E5%BF%83%E5%8A%A8-%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"怪你过分美丽.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E5%85%B6%E4%BB%96/%E6%80%AA%E4%BD%A0%E8%BF%87%E5%88%86%E7%BE%8E%E4%B8%BD.mp3","longth":""},{"name":"我 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E5%85%B6%E4%BB%96/%E6%88%91%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"探清水河.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E5%85%B6%E4%BB%96/%E6%8E%A2%E6%B8%85%E6%B0%B4%E6%B2%B3.mp3","longth":""},{"name":"晚婚.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E5%85%B6%E4%BB%96/%E6%99%9A%E5%A9%9A.mp3","longth":""},{"name":"梦一场.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E5%85%B6%E4%BB%96/%E6%A2%A6%E4%B8%80%E5%9C%BA.mp3","longth":""},{"name":"每天爱你多一些 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E5%85%B6%E4%BB%96/%E6%AF%8F%E5%A4%A9%E7%88%B1%E4%BD%A0%E5%A4%9A%E4%B8%80%E4%BA%9B%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"江湖儿女-陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E5%85%B6%E4%BB%96/%E6%B1%9F%E6%B9%96%E5%84%BF%E5%A5%B3-%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"浪子回头-陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E5%85%B6%E4%BB%96/%E6%B5%AA%E5%AD%90%E5%9B%9E%E5%A4%B4-%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"盛夏的果实-陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E5%85%B6%E4%BB%96/%E7%9B%9B%E5%A4%8F%E7%9A%84%E6%9E%9C%E5%AE%9E-%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"胡广生-陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E5%85%B6%E4%BB%96/%E8%83%A1%E5%B9%BF%E7%94%9F-%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"虚拟-陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E5%85%B6%E4%BB%96/%E8%99%9A%E6%8B%9F-%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"醒着醉-陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E5%85%B6%E4%BB%96/%E9%86%92%E7%9D%80%E9%86%89-%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"阿楚姑娘-陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E5%85%B6%E4%BB%96/%E9%98%BF%E6%A5%9A%E5%A7%91%E5%A8%98-%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""}],"原创":[{"name":"剑与家园 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E5%8E%9F%E5%88%9B/%E5%89%91%E4%B8%8E%E5%AE%B6%E5%9B%AD%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"告一段落 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E5%8E%9F%E5%88%9B/%E5%91%8A%E4%B8%80%E6%AE%B5%E8%90%BD%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"谁能没点病 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E5%8E%9F%E5%88%9B/%E8%B0%81%E8%83%BD%E6%B2%A1%E7%82%B9%E7%97%85%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"陈一发儿 - 弦上有春秋.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E5%8E%9F%E5%88%9B/%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF%20-%20%E5%BC%A6%E4%B8%8A%E6%9C%89%E6%98%A5%E7%A7%8B.mp3","longth":""},{"name":"陈一发儿 - 童话镇.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E5%8E%9F%E5%88%9B/%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF%20-%20%E7%AB%A5%E8%AF%9D%E9%95%87.mp3","longth":""},{"name":"陈一发儿 - 阿婆说.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E5%8E%9F%E5%88%9B/%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF%20-%20%E9%98%BF%E5%A9%86%E8%AF%B4.mp3","longth":""}],"无损":[{"name":"剑与家园.flac","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E6%97%A0%E6%8D%9F/%E5%89%91%E4%B8%8E%E5%AE%B6%E5%9B%AD.flac","longth":""},{"name":"弦上有春秋.flac","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E6%97%A0%E6%8D%9F/%E5%BC%A6%E4%B8%8A%E6%9C%89%E6%98%A5%E7%A7%8B.flac","longth":""},{"name":"童话镇.flac","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E6%97%A0%E6%8D%9F/%E7%AB%A5%E8%AF%9D%E9%95%87.flac","longth":""},{"name":"阿婆说.flac","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E6%97%A0%E6%8D%9F/%E9%98%BF%E5%A9%86%E8%AF%B4.flac","longth":""}],"翻唱":[{"name":"17.3.11陈一发儿-谢谢你的爱（爆刘继芬）.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/17.3.11%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF-%E8%B0%A2%E8%B0%A2%E4%BD%A0%E7%9A%84%E7%88%B1%EF%BC%88%E7%88%86%E5%88%98%E7%BB%A7%E8%8A%AC%EF%BC%89.mp3","longth":""},{"name":"Bizarre love triangle - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/Bizarre%20love%20triangle%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"City of Stars - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/City%20of%20Stars%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"Darling - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/Darling%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"Dying In The Sun - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/Dying%20In%20The%20Sun%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"Let her go - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/Let%20her%20go%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"New Soul - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/New%20Soul%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"Remember me - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/Remember%20me%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"When Christmas Comes To Town - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/When%20Christmas%20Comes%20To%20Town%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"lost stars - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/lost%20stars%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"一剪梅 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/%E4%B8%80%E5%89%AA%E6%A2%85%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"一次就好 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/%E4%B8%80%E6%AC%A1%E5%B0%B1%E5%A5%BD%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"下个星期去英国 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/%E4%B8%8B%E4%B8%AA%E6%98%9F%E6%9C%9F%E5%8E%BB%E8%8B%B1%E5%9B%BD%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"亲密爱人 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/%E4%BA%B2%E5%AF%86%E7%88%B1%E4%BA%BA%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"人间 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/%E4%BA%BA%E9%97%B4%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"你在终点等我 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/%E4%BD%A0%E5%9C%A8%E7%BB%88%E7%82%B9%E7%AD%89%E6%88%91%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"克卜勒 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/%E5%85%8B%E5%8D%9C%E5%8B%92%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"十万毫升泪水 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/%E5%8D%81%E4%B8%87%E6%AF%AB%E5%8D%87%E6%B3%AA%E6%B0%B4%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"历历万乡 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/%E5%8E%86%E5%8E%86%E4%B8%87%E4%B9%A1%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"天黑黑 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/%E5%A4%A9%E9%BB%91%E9%BB%91%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"奇妙能力歌 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/%E5%A5%87%E5%A6%99%E8%83%BD%E5%8A%9B%E6%AD%8C%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"她来听我的演唱会 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/%E5%A5%B9%E6%9D%A5%E5%90%AC%E6%88%91%E7%9A%84%E6%BC%94%E5%94%B1%E4%BC%9A%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"宝贝 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/%E5%AE%9D%E8%B4%9D%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"小幸运 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/%E5%B0%8F%E5%B9%B8%E8%BF%90%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"当你老了 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/%E5%BD%93%E4%BD%A0%E8%80%81%E4%BA%86%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"忽然之间 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/%E5%BF%BD%E7%84%B6%E4%B9%8B%E9%97%B4%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"性空山 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/%E6%80%A7%E7%A9%BA%E5%B1%B1%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"情人 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/%E6%83%85%E4%BA%BA%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"慢慢喜欢你 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/%E6%85%A2%E6%85%A2%E5%96%9C%E6%AC%A2%E4%BD%A0%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"成都 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/%E6%88%90%E9%83%BD%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"打错了 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/%E6%89%93%E9%94%99%E4%BA%86%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"木兰星 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/%E6%9C%A8%E5%85%B0%E6%98%9F%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"牵丝戏 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/%E7%89%B5%E4%B8%9D%E6%88%8F%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"矜持 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/%E7%9F%9C%E6%8C%81%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"笑红尘 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/%E7%AC%91%E7%BA%A2%E5%B0%98%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"董小姐 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/%E8%91%A3%E5%B0%8F%E5%A7%90%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"走马 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/%E8%B5%B0%E9%A9%AC%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"追梦人 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/%E8%BF%BD%E6%A2%A6%E4%BA%BA%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"遇见 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/%E9%81%87%E8%A7%81%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"陈一发儿 - can't take my eyes off you.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF%20-%20can%27t%20take%20my%20eyes%20off%20you.mp3","longth":""},{"name":"陈一发儿 - 写一首歌 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF%20-%20%E5%86%99%E4%B8%80%E9%A6%96%E6%AD%8C%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""},{"name":"鱼 - 陈一发儿.mp3","file":"https://fa-mama.oss-cn-beijing.aliyuncs.com/aizalmw/%E7%BF%BB%E5%94%B1/%E9%B1%BC%20-%20%E9%99%88%E4%B8%80%E5%8F%91%E5%84%BF.mp3","longth":""}]},
				current: 0,
				audioData: [{
						file: "http://app.tiantai.com.cn/uploads/20200819/a25100936ec5d372c6805e5b476dbd59.mp3",
						longth: "02:49",
						name: "",
					}
				],
				nowFullDuration: 0,
				duration: '100',
				currentTime: 60,
				audioDuration: ['0', '00'],
				audioCurTime: ['0', '00'],
				longth: '',
				isPlay: false,
				timer: null,
				system: '',
				styleObj: {
					borderRadius: '50%',
					height: '80rpx',
					width: '80rpx',
					position: 'absolute',
					top: '50%',
					left: '50%',
					transform: 'translate(-50%,-50% )',
					transformOrigin: 'center'
				},
				rotateTimer: null,
				rotateDeg: 0
			}
		},
		onUnload() {
			this.isPlay = false
			audioContext.destroy()
		},
		onLoad(e) {
			this.audioData = this.classMusicList.其他
			audioContext.src = this.audioData[0].file
			this.longth = this.audioData[0].longth
			this.system = uni.getSystemInfoSync().platform
			audioContext.onEnded((e) => {
				clearInterval(a.timer)
				clearInterval(a.rotateTimer)
				this.timer = null
				this.rotateTimer = null
				this.isPlay = false
				audioContext.seek(0);
				this.audioCurTime = ['0', '00']
				this.currentTime = 0
			})
			
			
		},
		onShow() {},
		onReady() {
			let a = this
			uni.setNavigationBarTitle({
				title: a.audioData[0].name
			});
			
			// 检查是否完播
			setInterval(function() {
				if (a.nowFullDuration < a.currentTime) {
					a.next();
				}
				
			}, 3000)
		},
		methods: {
			// audio player part : in this part we'd like to show the similar layouts and functions as wangyi music. created by Hsi (1563792476@qq.com)
			// in order to avoid syntax error of playing time , use backend data to show duration time instead of calculating by yourself
			changeAudio(info) {
				this.currentTime = 0
				this.isPlay = false
				clearInterval(this.timer)
				this.timer = null
				clearInterval(this.rotateTimer)
				this.rotateTimer = null
				this.longth = info.longth
				this.audioCurTime = ['0', '00']
				this.duration = 0
				audioContext.seek(0);
				audioContext.src = info.file
				uni.setNavigationBarTitle({
					title: info.name
				});
				this.playMusic()
			},
			next() {
				// next song function, the main thought of next song function is that we should find out the index of song which is playing. 
				// first of all , clear the timer and stop rotating cover, and then find out the index 
				clearInterval(this.rotateTimer)
				this.rotateTimer = null
				let src = audioContext.src
				this.current = 0
				//tips: (complex array may cause performance issues)
				this.audioData.filter((currentValue, index, arr) => {
					if (currentValue.file == src) {
						if (index + 1 >= arr.length) {
							clearInterval(this.timer)
							let timer = null
							this.isPlay = false;
							// once click next button , pause and reset playingtime 
							audioContext.seek(0);
							this.currentTime = 0
							this.audioCurTime = ['0', '00']
							audioContext.src = this.audioData[0].file
							this.longth = this.audioData[0].longth
							uni.setNavigationBarTitle({
								title: this.audioData[0].name
							})
						} else {
							clearInterval(this.timer)
							let timer = null
							this.isPlay = false;
							audioContext.seek(0);
							this.currentTime = 0
							this.audioCurTime = ['0', '00']
							audioContext.src = this.audioData[index + 1].file
							this.longth = this.audioData[index + 1].longth
							uni.setNavigationBarTitle({
								title: this.audioData[index + 1].name
							})
						}
					} else {}
				});
				this.playMusic()
			},
			pre() {
				clearInterval(this.rotateTimer)
				this.rotateTimer = null
				let src = audioContext.src
				this.current = 0
				this.audioData.filter((currentValue, index, arr) => {
					if (currentValue.file == src) {
						if (index == 0) {
							clearInterval(this.timer)
							let timer = null
							this.isPlay = false;
							audioContext.seek(0);
							this.currentTime = 0
							this.audioCurTime = ['0', '00']
							audioContext.src = this.audioData[arr.length - 1].file
							this.longth = this.audioData[arr.length - 1].longth
							uni.setNavigationBarTitle({
								title: this.audioData[arr.length - 1].name
							});
						} else {
							clearInterval(this.timer)
							let timer = null
							this.isPlay = false;
							audioContext.seek(0);
							this.currentTime = 0
							this.audioCurTime = ['0', '00']
							audioContext.src = this.audioData[index - 1].file
							this.longth = this.audioData[index - 1].longth
							uni.setNavigationBarTitle({
								title: this.audioData[index - 1].name
							});
						}
					} else {}
				});
				this.playMusic()
			},
			playMusic() {
				
				// reset duration time by clicking midbutton only 
				let duration = audioContext.duration;
				let currentTime = audioContext.currentTime;
				// 计算总时长
				audioContext.onCanplay(() => {
					this.nowFullDuration = audioContext.duration
				})
				this.duration = duration;
				this.currentTime = currentTime;
				this.audioCurTime[0] = Math.floor(Math.floor(currentTime) / 60);
				this.audioCurTime[1] = Math.floor(currentTime) % 60;
				if (this.isPlay) {
					this.isPlay = false;
					clearInterval(this.timer)
					clearInterval(this.rotateTimer)
					this.timer = null
					this.rotateTimer = null
					audioContext.pause();
				} else {
					this.isPlay = true;
					this.rotateTimer = setInterval(() => {
						this.rotateDeg++
						this.styleObj.transform = `translate(-50%,-50%) rotate(${this.rotateDeg}deg)`
					}, 50)
					audioContext.play();
					this.timer = setInterval(() => {
						this.currentTime++
						if (this.audioCurTime[1] > 58) {
							this.audioCurTime[0]++
							this.audioCurTime[1] = 0
							this.audioCurTime[1]++
						} else {
							this.audioCurTime[1]++
						}
					}, 1000)
				}
			},
			changeProgress(e) {
				// ios 拖动slider 会出现漂移，定位不准，苹果暂时用拖动时暂停播放来规避
				// ios pause music when dragging , Android could still play
				audioContext.seek(e.detail.value);
				// pause music when dragging , in order to load timer repeatly
				if (this.system == 'ios') {
					this.audioCurTime[0] = Math.floor(Math.floor(e.detail.value) / 60);
					this.audioCurTime[1] = Math.floor(e.detail.value) % 60;
					clearInterval(this.timer)
					clearInterval(this.rotateTimer)
					this.timer = null
					this.rotateTimer = null
					this.isPlay = false
					audioContext.pause();
				} else {
					clearInterval(this.timer)
					clearInterval(this.rotateTimer)
					this.timer = null
					this.rotateTimer = null
					this.isPlay = false;
					this.playMusic()
				}
			},
			// 分段器单击事件
			onClickItem(e) {
				if (this.current !== e.currentIndex) {
					this.current = e.currentIndex
					
					this.audioData = this.classMusicList[this.items[e.currentIndex]]
				}
			}
		}
	}
</script>

<style>
	@import url("index.css");
</style>
