<template>
<div class='tra-res'>
    <div class='search-head'>
        <div class='fir-line'>
           <header-top title="北京到上海" backRoute="/searchMain/train">
               <div slot="right"><span slot="right">右边</span></div>
               <div slot="title"><span>北京 <i class="iconfont icon-zhuanhuan"></i> 上海</span></div>
           </header-top>
        </div>
        <div class='sec-line'>
            <span @click="dateChange(false)">前一天</span>
              <transition name="togDate">
                <span v-if="selDate.day"  @click="$router.push('/calender')"><i class="iconfont icon-rili"></i> {{selDate.month + 1}}月{{selDate.day}}</span>
              </transition>
            <span @click="dateChange(true)">后一天</span>
        </div>
        <div class='res-type'>
        <ul>
            <li @click='activeIndex = index' v-for = 'type,index of types'>
                <a :class="{'active' : activeIndex === index}">{{type}}</a>
            </li>
        </ul>
    </div>
    </div>
    <div class='res-body'>
        <div class='tra-list' v-if='activeIndex === 0'>
            <div class='lists'>
                <!-- 对于获得到的数据进行循环 -->
                <div class='list' v-for="item,index of ticketRes" key="index">
                    <div class='tra-det'>
                         <div class="tra-start">
                            <p class='time start'><span>{{item.starttime}}</span></p>
                            <p class='city'>{{item.station}}</p>
                        </div>
                        <div>
                            <p class='area'>
                                <span class='timeArea'>{{item.costtime}}</span>
                                <span class='line'><i class="iconfont icon-tejiajipiaojiantou"></i></span>
                                <span class='trainNo'>{{item.trainno}}</span>
                            </p>
                        </div>
                        <div class="tra-end">
                            <p class='time end'><span>{{item.endtime}}</span></p>
                            <p class='city end'>{{item.endstation}}</p>
                        </div>
                        <div>
                            <p class='fare'>￥232起</p>
                        </div>
                    </div>
                    <div class='site'>
                  
                  
                        <p v-if="item.trainno.slice(0,1) === 'K'">
                            <span>硬座:{{showPrice ? ticketPrice(item.trainno) : item.yz}}</span>
                            <span>硬卧:{{showPrice ? ticketPrice(item.trainno) : item.yw}}</span>
                            <span>软卧:{{showPrice ? ticketPrice(item.trainno) : item.rw}}</span>
                            <span>无座:{{showPrice ? ticketPrice(item.trainno) : item.wz}}</span>
                        </p>
                        <p v-else>
                            <span>一等座:{{showPrice ? ticketPrice(item.trainno) : item.yd}}</span>
                            <span>二等座:{{showPrice ? ticketPrice(item.trainno) : item.ed}}</span>
                            <span>无座:{{showPrice ? ticketPrice(item.trainno) : item.wz}}</span>
                        </p>
                      
                    </div>
                </div>
            </div>
            <div class='filters'>
                <div><a @click="isPop = !isPop">高级筛选</a></div>
                <div><a :class="{'active' : filterAct }"  @click="filterType(true)">出发时间</a></div>
                <div><a :class="{'active' : !filterAct }"  @click="filterType(false)">旅行耗时</a></div>
                <div @click="showPrice = !showPrice"><a v-if="showPrice">显示价格</a><a v-else>显示余额</a></div>
            </div>
        </div>
        <div class='tran-list' v-if='activeIndex === 1'>
            
        </div>
        <div class='plane-list' v-if='activeIndex === 2'>
            
        </div>
    </div>
    <mt-popup  class="popUp" v-model="isPop" position="bottom">
        <div class="header">
            <span @click="popCancel">取消</span>
            <span class="filter">筛选</span>
            <span @click="popEnsure">确定</span>
        </div>
        <div class="body">
            <div >
                 <mt-checklist
                class="checklist"
                v-model="value"
                :options="['只看有票']">
            </mt-checklist>
            <mt-checklist
                class="checklist"
                v-model="value"
                :options="['只看直达']">
            </mt-checklist>
            </div>
            <div class="trainTys">
                <p>
                    <mt-checklist
                        class="checklist"
                        v-model="value"
                        :options="['高铁']">
                    </mt-checklist>
                    <mt-checklist
                        class="checklist"
                        v-model="value"
                        :options="['动车']">
                    </mt-checklist>
                    <mt-checklist
                        class="checklist"
                        v-model="value"
                        :options="['普通']">
                    </mt-checklist>
                    <mt-checklist
                        class="checklist"
                        v-model="value"
                        :options="['其他']">
                    </mt-checklist>
                </p>
            </div>
            <div class="station">
                <p>
                    <mt-checklist
                        class="checklist"
                        v-model="value"
                        :options="['南京']">
                    </mt-checklist>
                    <mt-checklist
                        class="checklist"
                        v-model="value"
                        :options="['其他']">
                    </mt-checklist>
                </p>
            </div>
            <div class="endstation">
                
            </div>
            <div class="timeRange">
                <mt-range v-model="rangeValue">
                    <div slot="start">开始时间</div>
                </mt-range>
                <mt-range 
                    :min="10"
                    :max="90"
                    :step="10" 
                    v-model="rangeValue">
                    
                </mt-range>
            </div>
        </div>
    </mt-popup>
</div>
</template>
<script type="text/javascript">
    import {mapActions, mapState} from 'vuex';
    // import {Indicator} from 'mint-ui';
    import headerTop from '../common/header';
    export default {
      name: 'traRes',
      components: {
        // Indicator
        headerTop
      },
      data () {
        return {
          filterAct: true,
          filters: {
          },
          rangeValue: '',
          value: '',
          types: ['火车', '中转', '机票'],
          activeIndex: '',
          result: '',
          stations: [],
          isPop: true,
          date: '2017-9-26',
          // 表示是否显示价格的布尔值
          showPrice: true
        };
      },
      filters: {
        toHour: function (val) {
          return `${val.slice(0, 2)}小时${val.slice(3)}分钟`;
        }
      },
      computed: {
        ...mapState(['ticketRes', 'lineRes', 'selDate', 'stationRes'])
      },
      methods: {
        ticketPrice (lineno) {
          console.log('去哦被');
          // this.getLineRes({trainno: lineno});
          console.log('我在这儿');
          console.log('车次信息', this.lineRes);
          // return this.lineRes
          return '';
        },
        filterType (time) {
          this.filterAct = time;
          console.log('得到的值', this.filterAct);
        },
        popCancel () {
          this.isPop = false;
        },
        dateChange (next) {
          let date = this.date.split('-');
          let day = date[2];
          let month = date[1];
          let year = date[0];
          if (next) {
            this.selDate.day ++;
            day++;
            console.log('yesss');
            this.date = `{${year}-${month}-${day}}`;
          } else {
            this.selDate.day --;
          }
          this.trainRes();
        },
        // 余票价格查询
        getResult () {
          this.getTicketRes({start: '北京', end: '上海', date: '2017-10-5'});
        },
        // 余票查询
        getResiduSite () {
        },
        // 站站票价查询, 用来查询票价
        // station: 开始车站， endStation: 结束车站, traninNo: 车次
        // 通过车站信息以及车次查找到对应车次的车票信息
        ...mapActions(['setStationRes', 'getTicketRes', 'setLineRes', 'getStationRes', 'getLineRes'])
      },
      mounted () {
        // this.getResult();
        setTimeout(function () {
          console.log('res', this.stationRes);
        }, 10);
      }
    };
</script>
<style  lang='stylus' scoped>
    .active
        display: inline-block;
        height: 2rem;
        line-height: 2rem;
        color: blue;
        border-bottom: 2px solid  #4C6198;
    .tra-res
        padding: 0;
        padding-top: 4rem;
      .search-head
          position: fixed;
          top: 0;
          left: 0;
          width:100%;
          background-color: #5495E6;
    .res-body
        margin-top: 7rem;
        margin-bottom: 4rem;      
    .search-head
        .sec-line
            padding: 1rem;
            height: 4rem;
            line-height: 2rem;
            display: flex;
            justify-content: space-around;
            span
                color: white;
                flex: 1;
            span:first-child
                text-align: left;
            span:last-child
                text-align: right;        
                    
        .res-type
            height: 2rem;
            background-color: white;
            overflow:hidden;
            ul
                display: flex;
               li
                 line-height: 2rem;
                 flex: 1;
                 display: inline-block;
                 text-align: center;
                 height: 2rem;
    .list
        padding: 0.5rem;
        margin: 0.5rem 0;
        background-color: white;
        .tra-det
            display: flex;
            box-sizing: content-box;
            .train-start
              p
                text-align: left ;
            .train-end
              text-align: right;     
            div
                flex: 1;
                width: 25%; 
                .time
                  span
                    font-size: 1.5rem;
                .start
                    color: #5495E6;  
                .fare
                  color: #FC6E51;
                  font-size: 1.3rem;    
                p
                    line-height: 2rem;
                    text-align: center;
                    height: 2rem;
                .area
                    >span
                        display: block;
                        height: 1rem;
                    .icon-tejiajipiaojiantou
                      height: 1.4rem;
                      font-size: 0.1rem;    
        .site
            width: 100%;
            p
                display: flex;
                span
                    flex: 1;
                    text-align: center;
                    height: 1.5rem;
                    line-height: 1.5rem;
    .filters
            width: 100%;
            background-color: #444854;
            position: fixed;
            bottom: 0;
            z-index: 100;
            padding: 0.5rem;
            display:flex;
            .active
                color: #5391DE;
            div
                text-align: center;
                padding: 0.3rem;
                flex: 1;
                a
                    padding-right: 0.5rem;
                    display: inline-block;
                    height: 1rem;
                    line-height: 1rem;
                    color: white;
                    &:first-of-type
                        border-right: 1px solid white;   
                &:last-of-type
                    a
                        border: none;            
                    
    .popUp
       width: 100%;
       .heaer,.body
           padding: 1rem;
       .header
           width: 100%;
           height: 3rem;
           background-color: #293340;
           display: flex;
           align-items:center;
           span
               flex: 1;
               color: white;
           .filter
               flex: 2;
               text-align: center;
               font-size: 1.2rem;                                                
        .body
            padding: 0 1.5rem;
            p
                .mint-cell-wrapper:last-child
                    text-align: left;
            .mint-checklist
                display: inline-block;
                width: 49%;
            .mint-checkbox-core
                border-radius: 5px !important;
                border: 2px solid skyblue !important; 
            .mint-cell-wrapper,.mint-cell
                background-image: none !important;
            .mint-cell
                text-align: left !important;    
    .togDate-enter
      transform: translateY(-10px);
    .togDate-enter-active, .togDate-leave
      translation: all 2s;              
                
                
            
                    
</style>