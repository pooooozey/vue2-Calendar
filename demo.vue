<template>
  <div>
			<input type="text" class="ipt ipt-time fl" @click.stop="calendarShow($event,{tag:'beginIpt'})" v-model="calendar.item.beginIpt.value" placeholder="默认全部">
			<span class="fl">-</span>
			<input type="text" class="ipt ipt-time fl" @click.stop="calendarShow($event,{tag:'endIpt'})" v-model="calendar.item.endIpt.value" placeholder="默认全部">

			<calendar 
				v-if="calendar.show"
		    :x="calendar.x" 
		    :y="calendar.y" 
		    :beginDate="calendar.beginDate" 
		    :endDate="calendar.endDate" 
		    :defDate="calendar.defDate"
		    :tag="calendar.tag"
		    @hideCalendar="hideCalendar"
		    @setIptDate="setIptDate"
			>
		  </calendar>
  </div>
</template>

<script>
import Calendar from "./Calendar.vue"

export default {
  data(){
		return {
			calendar:{
				defDate : '',//初始时间，默认今天
				beginDate : '',
				endDate : '',
				tag : null,//目标item的key
        x : 0,
        y : 0,
        show : false,
        item : {
        	beginIpt : {
						value : '',
						endTag : 'endIpt'
	        },
	        endIpt : {
						value : '',
						beginDate : '',
						beginTag : 'beginIpt'
	        }
        }
      }
		}
	},
  components : {
    Pagination,
    Calendar
  },
  
  methods:{
  	calendarShow(e,opt) {
  		this.calendar.tag = opt.tag
      this.calendar.x = e.target.offsetLeft
      this.calendar.y = e.target.offsetTop+e.target.offsetHeight+8
      this.calendar.show = true
      let item = this.calendar.item[opt.tag]
  		
      if(item.beginTag){
      	let tagItem = this.calendar.item[item.beginTag]
      	if(tagItem.value){
      		this.calendar.beginDate = tagItem.value
      		this.calendar.defDate = item.value||tagItem.value
      	}
      }
      if(item.endTag){
      	this.calendar.beginDate = ''
      	this.calendar.defDate = item.value
      }

  	},
  	hideCalendar(){
      this.calendar.show = false
  	},
  	setIptDate(opt){
  		this.calendar.item[opt.tag].value = opt.value
  		this.hideCalendar()
      let item = this.calendar.item[opt.tag]
  		if(item.endTag){
      	this.calendar.item[item.endTag].value = ''
      }
  	}
  }
}
</script>

<style scoped>

</style>
