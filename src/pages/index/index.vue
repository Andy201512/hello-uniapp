<template>
  <view>
    <view class="header">
      <uni-icons class="icon" type="person-filled" size="30"/>
      <input class="search" placeholder="请输入搜索内容"/>
      <uni-icons class="icon" type="email" size="30"/>
      <uni-icons class="icon" type="compose" size="30"/>
    </view>
    <view class="navigation">
      <scroll-view class="scroll-view_H" scroll-x="true" show-scrollbar="false">
        <text class="navigationItem">关注<uni-icons class="icon" type="bottom" size="10"/></text>
        <text class="navigationItem" v-for="item in navigationList">{{ item }}</text>
      </scroll-view>
      <uni-icons class="icon" type="bottom" size="15"/>
    </view>
    <view class="articleList">
      <scroll-view class="uni-list scroll-Y" scroll-y="true">
        <block v-for="(value, index) in listData" :key="index">
          <view class="uni-list-cell" hover-class="uni-list-cell-hover">
            <view class="uni-media-list">
              <image class="uni-media-list-logo" :src="value.cover"></image>
              <view class="uni-media-list-body">
                <view class="uni-media-list-text-top">{{ value.title }}</view>
                <view class="uni-media-list-text-bottom">
                  <text>{{ value.author_name }}</text>
                  <text>{{ value.published_at }}</text>
                </view>
              </view>
            </view>
          </view>
          <!-- #ifdef APP-PLUS -->
          <view class="ad-view" v-if="(index > 0 && (index+1) % 10 == 0)">
            <ad :adpid="adpid" @error="aderror"></ad>
          </view>
          <!-- #endif -->
        </block>
      </scroll-view>
    </view>
  </view>
</template>

<script>
  import { dateUtils } from  '../../common/util.js';

  export default {
    name: "",
    components: {},
    props: {},
    data() {
      return {
        navigationList: [
          '热门',
          '榜单',
          '新鲜事',
          '同城',
          '科技',
          '明星',
          '电影',
          '音乐',
          '数码',
          '汽车',
          '游戏',
        ],
        listData: [],
        reload: false,
      };
    },
    watch: {},
    computed: {},
    onLoad() {
			this.getList();
		},
    methods: {
      setTime: function(items) {
				var newItems = [];
				items.forEach(e => {
					newItems.push({
						author_name: e.author_name,
						cover: e.cover,
						id: e.id,
						post_id: e.post_id,
						published_at: dateUtils.format(e.published_at),
						title: e.title
					});
				});
				return newItems;
			},
      getList() {
				var data = {
					column: 'id,post_id,title,author_name,cover,published_at' //需要的字段名
				};
				if (this.last_id) {
					//说明已有数据，目前处于上拉加载
					this.status = 'loading';
					data.minId = this.last_id;
					data.time = new Date().getTime() + '';
					data.pageSize = 10;
				}
				uni.request({
					url: 'https://unidemo.dcloud.net.cn/api/news',
					data: data,
					success: data => {
						if (data.statusCode == 200) {
							let list = this.setTime(data.data);
							this.listData = this.reload ? list : this.listData.concat(list);
							this.last_id = list[list.length - 1].id;
							this.reload = false;
						}
					},
					fail: (data, code) => {
						console.log('fail' + JSON.stringify(data));
					}
				});
			},
    },
    created() {},
    mounted() {},
};
</script>

<style scoped>
  @import '../../common/uni-nvue.css';

  .header{
    display: flex;
    flex-direction: row;
  }
  .icon{
    flex: 1;
  }
  .search{
    height: 30px;
    flex: 5;
  }
  .navigation {
    display: flex;
    flex-direction: row;
    margin-top: 5px;
  }
  .scroll-view_H {
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    white-space: nowrap;
    width: 90%;
  }
  .navigationItem {
    margin-right: 20px;
    width: 50px;
    /* flex: 2; */
  }
  .scroll-Y {
    height: 500px;
  }
</style>