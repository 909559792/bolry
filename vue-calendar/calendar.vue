<template>
  <section class="by_container" :class="{'moreMark':isMoreMark}">
    <div class="by_content_all">
      <div class="year_panel vux-1px-b">
        <span class="left" @click="PreMonth"></span>
        <span class="year">{{dateTop}}</span>
        <span class="right" @click="NextMonth"></span>
      </div>
      <div class="by_content">
        <div class="by_content_item" v-for="(tag,index) in textTop" :key="index">
          <div>
            {{tag}}
          </div>
        </div>
      </div>
      <div class="by_content" :class="calenderClass">
        <div class="by_content_item" v-for="(item,index) in list" @click="clickDay(item,index)" :key="index" :class="{'active_line':isTodayInLine(index),'nLeft':index%7==0,'nRight':(index+1)%7==0}">
          <div>
            <li class="by_nextDayShow" v-if="(isHideOtherday&&item.nextDayShow)||item.otherMonth||item.dayHide" v-bind:class="[{isToday_now:item.isTodayNow},setClass(item)]">
              {{item.id}}
            </li>
            <li v-else v-bind:class="[{ by_isToday: item.isToday&&!isMoreMark,by_isMark:item.isMark,isTodayNow:item.isTodayNow},setClass(item)]">
              {{item.id}}
            </li>
          </div>
        </div>
      </div>
      <div class="change_size" :class="calenderClass" @click.stop="changeClass"></div>
    </div>
  </section>
</template>
<script>
  export default {
    data() {
      return {
        textTop: ['一', '二', '三', '四', '五', '六', '日'],
        myData: [],
        list: [],
        activeIndex: -1,
        calenderClass: '',
        dateTop: ''
      };
    },
    props: {
      date: {
        String
      },
      isMoreMark: {
        Boolean,
        default: false,
      },
      value: {
        String
      },
      size: {
        default: 'default'
      },
      markDate: {
        default: '[]'
      },
      markDateMore: {
        default: '[]'
      },
      agoDayHide: {
        default: '0'
      },
      futureDayHide: {
        default: '15181550670000'
      },
      isHideOtherday: {
        default: false
      }
    },
    created() {
      this.myData = new Date();
    },
    methods: {
      preDay() {
        let tempDate = new Date(this.list[this.activeIndex].date);
        tempDate = tempDate.setDate(tempDate.getDate() - 1);
        let dateFormatSting = this.dateFormat(tempDate);
        this.ChoseMonth(dateFormatSting);
      },
      nextDay() {
        let tempDate = new Date(this.list[this.activeIndex].date);
        tempDate = tempDate.setDate(tempDate.getDate() + 1);
        let dateFormatSting = this.dateFormat(tempDate);
        this.ChoseMonth(dateFormatSting);
      },
      changeClass() {
        this.calenderClass = this.calenderClass == 'mini' ? 'default' : 'mini';
        this.$emit('update:size', this.calenderClass);
      },
      isTodayInLine(index) {
        let i = ~~((this.activeIndex + 1) / 7);
        (this.activeIndex + 1) % 7 != 0 && (i += 1);
        let j = ~~((index + 1) / 7);
        (index + 1) % 7 != 0 && (j += 1);
        return j == i
      },
      setClass(data) {
        let obj = {};
        obj[data.markClassName] = data.markClassName;
        return obj;
      },
      clickDay: function (item, index) {
        this.ChoseMonth(item.date);
      },
      ChoseMonth: function (date, isChosedDay = true) {
        this.$emit('input', date);
        date = this.dateFormat(date);
        this.myData = new Date(date);
        this.$emit('changeMonth', this.dateFormat(this.myData));
        this.getList(this.myData, date, isChosedDay);
      },
      PreMonth: function (date, isChosedDay = true) {
        date = this.dateFormat(date);
        this.myData = this.getPreMonth(this.myData);
        this.$emit('changeMonth', this.dateFormat(this.myData));
        this.getList(this.myData, date, isChosedDay);
      },
      NextMonth: function (date, isChosedDay = true) {
        date = this.dateFormat(date);
        this.myData = this.getNextMonth(this.myData);
        this.$emit('changeMonth', this.dateFormat(this.myData));
        this.getList(this.myData, date, isChosedDay);
      },
      getPreMonth: function (date) {
        let timeArray = this.dateFormat(date).split('/');
        let year = timeArray[0];
        let month = timeArray[1];
        let day = timeArray[2];
        let days = new Date(year, month, 0);
        days = days.getDate();
        let year2 = year;
        let month2 = parseInt(month) - 1;
        if (month2 == 0) {
          year2 = parseInt(year2) - 1;
          month2 = 12;
        }
        let day2 = day;
        let days2 = new Date(year2, month2, 0);
        days2 = days2.getDate();
        if (day2 > days2) {
          day2 = days2;
        }
        if (month2 < 10) {
          month2 = '0' + month2;
        }
        if (day2 < 10) {
          day2 = '0' + day2;
        }
        let t2 = year2 + '/' + month2 + '/' + day2;
        return new Date(t2);
      },
      getNextMonth: function (date) {
        let arr = this.dateFormat(date).split('/');
        let year = arr[0]; //获取当前日期的年份
        let month = arr[1]; //获取当前日期的月份
        let day = arr[2]; //获取当前日期的日
        let days = new Date(year, month, 0);
        days = days.getDate(); //获取当前日期中的月的天数
        let year2 = year;
        let month2 = parseInt(month) + 1;
        if (month2 == 13) {
          year2 = parseInt(year2) + 1;
          month2 = 1;
        }
        let day2 = day;
        let days2 = new Date(year2, month2, 0);
        days2 = days2.getDate();
        if (day2 > days2) {
          day2 = days2;
        }
        if (month2 < 10) {
          month2 = '0' + month2;
        }
        if (day2 < 10) {
          day2 = '0' + day2;
        }
        let t2 = year2 + '/' + month2 + '/' + day2;
        return new Date(t2);
      },
      getDaysInOneMonth: function (date) { //当前月的天数
        let getyear = date.getFullYear();
        let getmonth = date.getMonth() + 1;
        let d = new Date(getyear, getmonth, 0);
        return d.getDate();
      },
      getMonthweek: function (date) { //向前空几个
        let getyear = date.getFullYear();
        let getmonth = date.getMonth() + 1;
        let dateOne = new Date(getyear + '/' + getmonth + '/1');
        return dateOne.getDay() == 0 ? 6 : dateOne.getDay() - 1;
      },
      getList: function (date, chooseDay, isChosedDay = true) {
        var that = this;
        const leftArr = that.getLeftArr(date);
        const mygetMonth = date.getMonth() + 1 < 10 ? '0' + (date.getMonth() + 1) : date.getMonth() + 1;
        that.dateTop = date.getFullYear() + '年' + mygetMonth + '月';
        let array = [];
        const onMonthDays = that.getDaysInOneMonth(date);
        for (let i = 0; i < onMonthDays; i++) {
          let M = (date.getMonth() + 1) < 10 ? `0${(date.getMonth() + 1)}` : (date.getMonth() + 1);
          let D = (i + 1) < 10 ? `0${(i + 1)}` : (i + 1);
          const nowTime = date.getFullYear() + '-' + M + '-' + D;
          let markClassName = "";
          let len = that.markDateMore.findIndex(item => {
            return item.date == nowTime
          })
          len != -1 && (markClassName = that.markDateMore[len].className)
          i == 0 && (markClassName += 'one ');
          (i + 1) == onMonthDays && (markClassName += 'last ')
          let listObj = {
            id: i + 1,
            date: nowTime,
            isMark: that.markDate.indexOf(nowTime) >= 0,
            dayHide: new Date(nowTime).getTime() / 1000 < parseInt(that.agoDayHide) || new Date(nowTime).getTime() /
              1000 > parseInt(that.futureDayHide),
            markClassName: markClassName,
            nextDayShow: new Date(nowTime).getTime() >
              new Date().getTime()
          }
          if (that.dateFormat(that.value) == that.dateFormat(new Date(nowTime))) {
            listObj = Object.assign(listObj, {
              isTodayNow: true,
              isToday: true,
            })
            that.activeIndex = leftArr.length + i;
            that.$emit(
              'isToday',
              that.dateFormat(nowTime)
            );
          } else {
            listObj = Object.assign(listObj, {
              isTodayNow: false,
              isToday: chooseDay == nowTime && isChosedDay
            })
          }
          array.push(listObj);
        }
        const rightArr = this.getRightArr(date, array);
        array = [...leftArr, ...array, ...rightArr];
        this.list = array;
      },
      getLeftArr: function (date) {
        let array = [];
        const leftNum = this.getMonthweek(date);
        const num = this.getDaysInOneMonth(this.getPreMonth(date)) - leftNum + 1;
        const preDate = this.getPreMonth(date);
        //上个月多少开始
        for (let i = 0; i < leftNum; i++) {
          const nowTime = preDate.getFullYear() + '/' + (preDate.getMonth() + 1) + '/' + (num + i);
          array.push({
            id: num + i,
            date: nowTime,
            dayHide: new Date(nowTime).getTime() / 1000 < parseInt(this.agoDayHide) || new Date(nowTime).getTime() /
              1000 > parseInt(this.futureDayHide),
            nextDayShow: new Date(nowTime).getTime() >
              new Date().getTime(),
            otherMonth: -1
          });
        }
        return array;
      },
      getRightArr: function (date, arr) {
        let array = [];
        const nextDate = this.getNextMonth(date);
        const _length = 7 - arr.length % 7;
        //向后添加数据
        if (_length < 7) {
          for (let i = 0; i < _length; i++) {
            const nowTime = nextDate.getFullYear() + '/' + (nextDate.getMonth() + 1) + '/' + (i + 1);
            array.push({
              id: i + 1,
              date: nextDate.getFullYear() + '/' + (nextDate.getMonth() + 1) + '/' + (i + 1),
              dayHide: new Date(nowTime).getTime() / 1000 < parseInt(this.agoDayHide) || new Date(nowTime).getTime() /
                1000 > parseInt(this.futureDayHide),
              nextDayShow: new Date(nowTime).getTime() >
                new Date().getTime(),
              otherMonth: 1
            });
          }
        }
        return array;
      },
      dateFormat: function (date) {
        date = new Date(date)
        return date.getFullYear() + '/' + (date.getMonth() + 1) + '/' + date.getDate();
      },
      setNewMarkDateMore(val) {

      }
    },
    mounted() {
      !this.value && this.$emit('input', this.dateFormat(new Date()).replace(/\//g, '-'))
      this.getList(this.myData);
      this.calenderClass = this.size;
    },
    watch: {
      dateTop(v) {
        let date = v.replace("年", "-").replace("月", "");
        this.$emit("update:date", date);
      },
      calenderClass(v) {
        this.$emit('update:size', v);
      },
      markDate(val, oldVal) {
        this.getList(this.myData);
      },
      markDateMore(val, oldVal) {
        this.getList(this.myData);
      }
    }
  };

</script>
<style lang="less">
  .by_container {
    .year_panel {
      height: 100px;
      line-height: 100px;
      font-size: 30px;
      color: #333;
      text-align: center;
      .year {
        padding: 0px 40px;
      }
      span {
        display: inline-block;
        height: 40px;
        line-height: 40px;
        cursor: pointer;
      }
      .left,
      .right {
        position: relative;
        width: 80px;
        height: 40px;
        vertical-align: middle;
        &::before {
          position: absolute;
          top: 50%;
          right: 10px;
          display: block;
          content: "";
          width: 0;
          height: 0;
          border-left: 8px solid transparent;
          border-right: 8px solid transparent;
          border-top: 16px solid #bfbfbf;
          transform: rotate(90deg) translateY(-50%);
          margin-top: -7px;
        }
      }
      .right {
        &::before {
          left: 0;
          transform: rotate(-90deg);
        }
      }
    }
  }

  .moreMark {
    .nRight {
      li {
        &::before {
          border-radius: 0px 18px 18px 0px;
        }
      }
    }
    .nLeft {
      li {
        &::before {
          border-radius: 18px 0px 0px 18px;
        }
      }
    }
    .one {
      &::before {
        border-radius: 18px 0px 0px 18px;
      }
    }
    .last {
      &::before {
        border-radius: 0px 18px 18px 0px;
      }
    }
    .nRight {
      .one {
        &::before {
          border-radius: 18px;
          content: '';
        }
      }
    }
    .nLeft {
      .last {
        &::before {
          border-radius: 18px;
          content: '';
        }
      }
    }
    .by_content_item {
      li {
        &::before {
          display: block;
          content: '';
          position: absolute;
          width: 100%;
          height: 25px;
          background-color: #b7b7b7;
          left: 0;
          bottom: -8px;
        }
      }

      .by_nextDayShow {
        &::before {
          display: none;
        }
      }
    }
  }

  .by_container {
    position: relative;
    margin: auto;
    transition: all .3s ease-in-out;
  }

  .moreMark {
    .by_content_all {
      padding-bottom: 30px;
    }
    .by_content.default,
    .by_content.mini {
      .by_content_item {
        padding-bottom: 12.34%;
      }
    }
  }

  li {
    list-style-type: none;
  }

  .change_size.default {
    transform: rotate(180deg);
  }

  .change_size {
    z-index: 1;
    transition: all .3s step-start;
    position: absolute;
    bottom: -20px;
    right: 80px;
    height: 40px;
    width: 40px;
    border-radius: 50%;
    background-color: white;
    &::before {
      position: absolute;
      top: 20px;
      left: 0px;
      margin-left: 14px;
      display: block;
      content: "";
      width: 0;
      height: 0;
      border-left: 6px solid transparent;
      border-right: 6px solid transparent;
      border-top: 6px solid #ccc;
    }
  }

  .by_content_all {
    font-family: -apple-system, BlinkMacSystemFont, 'PingFang SC', 'Helvetica Neue', STHeiti, 'Microsoft Yahei', Tahoma,
    Simsun, sans-serif;
    background-color: white;
    width: 100%;
    overflow: hidden;
    padding-bottom: 10px;
  }

  .by_content {
    display: flex;
    flex-wrap: wrap;
    padding: 0 3% 0 3%;
    width: 100%;
  }

  .default {
    .by_content_item {
      display: block;
    }
  }

  .mini {
    .by_content_item {
      display: none;
    }
    .by_content_item.active_line {
      display: block;
    }
  }

  .by_content:first-child .by_content_item {
    color: #ddd;
    font-size: 18px;
    height: 15%;
    line-height: 15%;
  }

  .by_content_item {
    font-size: 30px;
    width: 14.2%;
    padding-bottom: 10.14%;
    text-align: center;
    color: #333;
    position: relative;
  }

  .by_content_item div {
    position: absolute;
    top: 0;
    width: 100%;
    height: 100%;
    left: 0;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .by_content_item li {
    width: 50px;
    height: 50px;
    border-radius: 100px;
    line-height: 50px;
  }

  @media screen and (min-width: 460px) {
    .by_content_item li:hover {
      cursor: pointer
    }
  }

  .by_top_changge {
    display: flex;
    padding: 10px 0px;
  }

  .by_top_changge li {
    cursor: pointer;
    display: flex;
    color: #333;
    font-size: 18px;
    flex: 1;
    justify-content: center;
    align-items: center;
    height: 47px;
  }

  .by_top_changge .by_content_li {
    font-size: 28px;
    color: #333;
    cursor: auto;
    flex: 2.5;
  }

  .by_jiantou1 {
    width: 20px;
    height: 20px;
    border-top: 2px solid #333;
    border-left: 2px solid #333;
    transform: rotate(-45deg);
  }

  .by_jiantou1:active,
  .by_jiantou2:active {
    border-color: #ddd;
  }

  .by_content_item div .by_isToday {
    margin: auto;
    background-color: #ff9c00;
    color: white;
    border-radius: 100px;
    text-align: center;
  }

  .by_jiantou2 {
    width: 20px;
    height: 20px;
    border-top: 2px solid #333;
    border-right: 2px solid #333;
    transform: rotate(45deg);
  }

  .by_content_item div .by_isMark {
    position: relative;
    &::before {
      position: absolute;
      display: block;
      content: '';
      height: 10px;
      width: 10px;
      border-radius: 50%;
      background-color: #ff9c00;
      bottom: 0px;
      left: 50%;
      margin-left: -5px;
    }
  }

  .by_content_item div .by_nextDayShow {
    color: #bfbfbf;
  }

</style>
