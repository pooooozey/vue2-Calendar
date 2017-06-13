<template>
  <div class="calendar" :style="{left:x+'px',top:y+'px'}" @click.stop="empty">
    <div class="calendar-tools">
    	<div class="tools-item">
    		<span class="prv" @click="lastYear">&lt;</span>
    		<span class="next" @click="nextYear">&gt;</span>
    		<div class="text"><span>{{curYear}}</span>年</div>
    	</div>
    	<div class="tools-item">
    		<span class="prv" @click="lastMonth">&lt;</span>
    		<span class="next" @click="nextMonth">&gt;</span>
    		<div class="text"><span>{{curMonth+1}}</span>月</div>
    	</div>
    </div>

    <div class="calendar-head">
    	<div class="week">日</div>
			<div class="week">一</div>
			<div class="week">二</div>
			<div class="week">三</div>
			<div class="week">四</div>
			<div class="week">五</div>
			<div class="week">六</div>
    </div>
    <div class="calendar-main">
    	<div class="day" v-for="item in day">
    		<span :class="{disabled:item.disabled}" @click="changeCurDate(item.val,item.disabled)">{{item.val}}</span>
    	</div>
    </div>

  </div>
</template>

<script>
export default {
	props : ['x','y','beginDate','endDate','defDate','show','tag'],
	data(){
		return {
			begin : this.beginDate,
			end : this.endDate,
			defaultDate : this.defDate,
			curYear : 0,
			curMonth : 0,
			curDay : 0,
			beginYear : 0,
			beginMonth : 0,
			beginDay : 0,
			day : null//放本月日子
		}
	},
	mounted(){
		this.getCurDate()
		document.addEventListener("click",this.hideCalendar)
	},
	methods:{
		hideCalendar(){
			this.$emit("hideCalendar")
			document.removeEventListener("click",this.hideCalendar)
		},
  	isLeapYear(year){
		  return (year % 4 == 0 || (year % 100 == 0 && year % 400 == 0))
		},
		getCurDate(){
			//根据传的日期字符串设置初始日期
			let dateArr = null
			if(this.defaultDate){
				let symbol = this.defaultDate[4]
				dateArr = this.defaultDate.split(symbol)
				this.curYear = parseInt(dateArr[0])
				this.curMonth = parseInt(dateArr[1])-1
				this.curDay = parseInt(dateArr[2])
			}else{
				let now = new Date()
				this.curYear = now.getFullYear()
				this.curMonth = now.getMonth()
				this.curDay = now.getDate()
			}
			if(this.beginDate){
				let symbol = this.beginDate[4]
				dateArr = this.beginDate.split(symbol)
				this.beginYear = parseInt(dateArr[0])
				this.beginMonth = parseInt(dateArr[1])-1
				this.beginDay = parseInt(dateArr[2])
			}
			this.setCurDate()
		},
		setCurDate(){
			
		  this.createDay(this.changeDay(this.curYear,this.curMonth))
		},
		changeDay(year,month){
			let monthDays = [31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31]

			let dayNum = monthDays[month]
			if (month == 1 && this.isLeapYear(year)) {  
	      dayNum++
	    }
	    return dayNum
			

		},
		getLastMonth(year,month){
			if(month-1<=0){
				month = 11
				year--
			}else{
				month--
			}
			return this.changeDay(year,month)
		},
		getNextMonth(year,month){
			if(month+1>=12){
				month = 0
				year++
			}else{
				month++
			}
			return this.changeDay(year,month)
		},
		createDay(dayNum){
			let arr = []
			let curDay = new Date(this.curYear,this.curMonth,1)
			let curWeek = curDay.getDay()
			let lastMonthDays = this.getLastMonth(this.curYear,this.curMonth)
			let nextMonthDays = this.getNextMonth(this.curYear,this.curMonth)
			

			for(let i=curWeek-1;i>=0 ;i--){
				arr.push({
					val : lastMonthDays-i,
					disabled : true
				})
			}

			let disDayNum = this.disDay(dayNum)

			console.log(disDayNum,dayNum)

			for(let i=0;i<disDayNum ;i++){
				arr.push({
					val : i+1,
					disabled : true
				})
			}

			for(let i=disDayNum;i<dayNum ;i++){
			console.log(dayNum-disDayNum,disDayNum)
				arr.push({
					val : i+1,
					disabled : false
				})
			}

			if((dayNum+curWeek)%7!==0){
				for(let i=0;i<7-(dayNum+curWeek)%7 ;i++){
					arr.push({
						val : i+1,
						disabled : true
					})
				}
			}
			
			this.day = arr

		},
		disDay(dayNum){
			if(!this.beginDate){
				//没有
				return 0
			}
			console.log(this.beginYear,this.curYear)
			console.log(this.beginMonth,this.curMonth)
			console.log(this.beginDay)
			if(this.beginYear>this.curYear){
				//all
				return dayNum
			}else if(this.beginYear===this.curYear){
				if(this.beginMonth>this.curMonth){
					//all
					return dayNum
				}else if(this.beginMonth===this.curMonth){
					//this.beginDay
					return this.beginDay
				}
			}
			return 0
		},
		lastMonth(){
			if(this.curMonth-1<=0){
				this.curMonth = 11
				this.curYear--
			}else{
				this.curMonth--
			}
		  this.createDay(this.changeDay(this.curYear,this.curMonth))
		},
		nextMonth(){
			if(this.curMonth+1>=12){
				this.curMonth = 0
				this.curYear++
			}else{
				this.curMonth++
			}
		  this.createDay(this.changeDay(this.curYear,this.curMonth))
		},
		lastYear(){
			this.curYear--
		  this.createDay(this.changeDay(this.curYear,this.curMonth))
		},
		nextYear(){
			this.curYear++
		  this.createDay(this.changeDay(this.curYear,this.curMonth))

		},
		changeCurDate(day,disabled){
			//点击选择时间
			if(!disabled){
				this.curDay = day
				this.$emit("setIptDate",{
					tag : this.tag,
					value : this.curYear+'-'+(this.curMonth+1)+'-'+this.curDay
				})
			}
			
		},
		empty(){}
  }
}
</script>

<style scoped>
.calendar{
	width:294px;
	padding:10px;
	background:#fff;
	border:1px solid #dedede;
	border-radius:2px;
	opacity:.95;
	position:absolute;
	left:500px;
	top:250px;
	z-index:5;
	tansition:.5s;
}
.calendar:before{
	content:'';
	position:absolute;
	left:30px;
	top:-10px;
	border:5px solid transparent;
	border-bottom-color:#dedede;
}
.calendar-tools{
	overflow:hidden;
	height:40px;
	line-height:34px;
	color:#666;
	font-size:20px;
}
.tools-item{
	width:50%;
	text-align:center;
	float:left;
	background:#337ab7;
	color:#fff;

}
.tools-item .prv{
	width:30px;
	height:100%;
	display:inline-block;
	float:left;
	cursor:pointer;
}
.tools-item .next{
	width:30px;
	height:100%;
	display:inline-block;
	float:right;
	cursor:pointer;
}
.tools-item .prv:hover,.tools-item .next:hover{
	background:#155e9c;
}
.calendar-head{
	overflow:hidden;
	color:#337ab7;
}
.calendar-head .week{
	width:42px;
	height:42px;
	line-height:42px;
	text-align:center;
	font-size:16px;
	float:left;
}
.calendar-main .day{
	width:42px;
	height:42px;
	line-height:42px;
	text-align:center;
	font-size:14px;
	float:left;
}
.calendar-main .day span{
	height:36px;
	line-height:36px;
	display:block;
	margin:2px;
	border-radius:2px;
	cursor:pointer;
}
.calendar-main .disabled{
	color:#999;
}
.calendar-main .day span:hover{
	background:#f1f1f1;
}
.calendar-main .disabled:hover{
	background:none !important;
	cursor:default !important;
}
.calendar-main .day.active span{
	background:#337ab7;
	color:#fff;
}




</style>
