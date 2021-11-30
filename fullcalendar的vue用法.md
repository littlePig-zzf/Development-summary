在vue的单页面使用fullcalendar，本文用自定义头部来做实例进行分享。

这个是Fullcalendar的例子，我最近找到的一个很好参考的demo，可以去看看。https://codesandbox.io/search?refinementList%5Btemplate%5D=&refinementList%5Bnpm_dependencies.dependency%5D%5B0%5D=@fullcalendar/vue&page=2&configure%5BhitsPerPage%5D=12



一、首先安装fullcalendar的依赖包，https://fullcalendar.io/docs/plugin-index 这个地址是插件合集。

@fullcalendar/interaction 这个插件主要是用户事件的拖拽交互。

```
yarn add --save @fullcalendar/daygrid
yarn add --save @fullcalendar/list
yarn add --save @fullcalendar/timegrid
yarn add --save @fullcalendar/interaction
```



二、安装完依赖插件， 编写自定义头部、插入fullcalendar插件![自定义日历头部](E:\自定义日历头部.png)



```
<template>
   <div>
        <div class="fc-toolbar">
            <div class="fc-left">
                <el-button-group>
                    <el-button @click="month">
                        月
                    </el-button>
                    <el-button @click="week">
                        周
                    </el-button>
                    <el-button @click="today">
                        今天
                    </el-button>
                </el-button-group>
            </div>
            <div class="fc-center">
                <el-button @click="prev">
                    上一个
                </el-button>
                <h2>
                    {{ title }}
                </h2>
                <el-button @click="next">
                    下一个
                </el-button>
            </div>
            <el-button ref="next"
                       class="search"
                       type="button"
                       @click="search">
                查询
            </el-button>
        </div>

        <FullCalendar
            id="calendar"
            ref="fullCalendar"
            class="demo-app-calendar"
            :options="calendarOptions">
            <template v-slot:eventContent="arg">
                <el-popover
                    placement="top-start"
                    title="标题"
                    width="200"
                    :visible-arrow="false"
                    trigger="hover">
                    <i class="title">{{ arg.event.title }}</i>
                    <el-button @click="more">
                        更多
                    </el-button>
                    <div slot="reference"
                         class="popper-content">
                        <span>{{ arg.timeText }}</span>
                        <i>{{ arg.event.title }}</i>
                        <i>{{ arg.event.title }}</i>
                    </div>
                </el-popover>
            </template>
        </FullCalendar>
    </div>
</template>
```

我这边样式比较简陋， 大伙可自行优化样式哈~



三、初始化fullcalendar的属性：

```
<script>
import FullCalendar from '@fullcalendar/vue';
import dayGridPlugin from '@fullcalendar/daygrid';
import timeGridPlugin from '@fullcalendar/timegrid';
import listPlugin from '@fullcalendar/list';
import interactionPlugin from '@fullcalendar/interaction';

export default {
    components: {
        FullCalendar, // make the <FullCalendar> tag available
    },
    data() {
        return {
            title: '',
            calendarOptions: {
                locale: 'zh',
                plugins: [dayGridPlugin, timeGridPlugin, listPlugin, interactionPlugin],
                initialView: 'dayGridMonth',
                editable: true,
                dayMaxEvents: true, // allow "more" link when too many events
                eventDurationEditable: true, // 可以调整事件的时间
                selectable: true, // 日历格子可选择
                nowIndicator: true, // 当前的时间线显示
                eventDisplay: 'block', // 争对全天的情况下，以块状显示
                headerToolbar: false, // 隐藏头部的导航栏
                selectMirror: false,
                displayEventEnd: true, // like 08:00 - 13:00
                eventTimeFormat: { // like '14:30:00'
                    hour: '2-digit',
                    minute: '2-digit',
                    meridiem: false,
                    hour12: false, // 设置时间为24小时
                },
                slotLabelFormat: { // 左侧时间格式
                    hour: '2-digit',
                    minute: '2-digit',
                    meridiem: 'lowercase',
                    hour12: false, // false设置时间为24小时
                },
                events: [
                    {
                        id: 1,
                        title: 'event 1',
                        start: '2021-10-21',
                        color: 'purple',
                    },
                    {
                        id: 2, title: 'event 2', start: '2021-10-22', color: 'purple',
                    },
                    { title: 'event 3', start: '2021-10-23' },
                    { title: 'event 4', start: '2021-10-24' },
                    { title: 'event 5', start: '2021-10-21' },
                    { title: 'event 6', start: '2021-10-21', color: 'purple' },
                    { title: 'event 7', start: '2021-10-21' },
                    {
                        id: 3,
                        title: 'event 6',
                        start: '2021-11-21',
                        end: '2021-11-23',
                        color: 'purple',
                        extendedProps: {
                            description: 'asdasdasdasdasdasdasdasds',
                        },
                        allDay: false,
                    },
                    {
                        id: 4,
                        title: 'event 7',
                        start: '2021-11-21',
                        extendedProps: {
                            description: '444444444444',
                        },
                    },
                ],
                eventColor: '#378006', // 事件的颜色
                allDayText: '全天',
                droppable: true, // this allows things to be dropped onto the calendar
            },
            calendarApi: null,
            monthEvent: [
                {
                    id: 'number_1',
                    resourceId: 'number_1',
                    title: 'event 1',
                    start: '2021-10-21',
                    color: 'purple',
                },
                {
                    resourceId: 'number_2',
                    id: 'number_2',  title: 'event 2', start: '2021-10-22', color: 'purple',
                },
                { title: 'event 3', start: '2021-10-23' },
                { title: 'event 4', start: '2021-10-24' },
                { title: 'event 5', start: '2021-10-21' },
                { title: 'event 6', start: '2021-10-21', color: 'purple' },
                { title: 'event 7', start: '2021-10-21' },
                {
                    id: 'number_3',
                    resourceId: 'number_3',
                    title: 'event 6',
                    start: '2021-11-21',
                    end: '2021-11-21',
                    color: 'purple',
                    extendedProps: {
                        description: 'asdasdasdasdasdasdasdasds',
                    }
                },
                {
                    id: 4,
                    title: 'event 7',
                    start: '2021-11-22',
                    extendedProps: {
                        description: '444444444444',
                    },
                },
            ],
            weekEvent: [
                {
                    id: 'number_1',
                    resourceId: 'number_1',
                    title: 'week_event',
                    start: '2021-11-11',
                    color: 'purple',
                },
            ]
        };
    },
    mounted() {
        this.calendarApi = this.$refs.fullCalendar.getApi();
        this.title = this.calendarApi.view?.title;
        // 模拟动态获取数据
        this.getDtata();
    },
    watch: {
        // 切换视图显示不同的事件
        'calendarApi.view.type'() {
            this.getDtata()
        }
    },
    methods: {
        getDtata() {
            setTimeout(() => {
                this.calendarOptions.events = this.calendarApi.view?.type === 'dayGridMonth' ?  this.monthEvent : this.weekEvent
            }, 200);
        },
        prev() {
            this.calendarApi.prev();
            this.title = this.calendarApi.view?.title;
        },
        next() {
            this.calendarApi.next();
            this.title = this.calendarApi.view?.title;
        },
        today() {
            this.calendarApi.today();
            this.title = this.calendarApi.view?.title;
        },
        month() {
            this.calendarApi.changeView('dayGridMonth');
            this.calendarApi.today();
            this.title = this.calendarApi.view?.title;
        },
        week() {
            this.calendarApi.changeView('timeGridWeek');
            this.calendarApi.today();
            this.title = this.calendarApi.view?.title;
        },
        day() {
            this.calendarApi.today();
            this.title = this.calendarApi.view?.title;
        },
        search() {
            this.calendarApi.changeView('dayGrid', {
                start: '2021-10-21',
                end: '2021-10-23',
            });
        },
    },
};
</script>
```



四、现在自定义头部的功能大概已经完成，根据点击 月 周 今天，调用对应的getApi()方法 去 changeView视图。



那么接下来开始写咱们的功能啦， 实现日历的单击、双击功能。

**功能点一： 日历的单击、双击功能、**

思路： 

```
直接使用日期的单击事件dateClick， 通过定时器来判断是否双击 还是单击事件。

直接在我们的data的calendarOptions对象里面配置这个属性：

let clickCount = 0;
let prev = ''; // 上一次点击的dom节点

data() {
    return {
        calendarOptions: {
        	...
        	dateClick: this.handleEventClick,
        }
    }
}

 methods: {
 	......
    handleEventClick() {
        if (e.dateStr !== prev) {
            clickCount = 0;
        }
        clickCount += 1;
        prev = e.dateStr;
        setTimeout(() => {
            if (clickCount === 2) {
                console.log('db click');
            } else if (clickCount === 1) {
                console.log('one click');
            }
            clickCount = 0;
        }, 300)
    },
}

```



**功能点二：移到事件上显示弹框并操作**

![点击事件弹窗显示](E:\点击事件弹窗显示.png)

更推荐思路一：通过插槽slot的方式去写

```
<template>
    <!-- 自定义头部的代码省略 -->

     <FullCalendar
        id="calendar"
        ref="fullCalendar"
        class="demo-app-calendar"
        :options="calendarOptions">
        <template v-slot:eventContent="arg">
            <el-popover
                placement="top-start"
                title="标题"
                width="200"
                :visible-arrow="false"
                trigger="hover">
                <i class="click">{{ arg.event.title }}</i>
                <el-button @click="handleMore">
                    更多
                </el-button>
                <div slot="reference"
                     class="popper-content">
                    <span>{{ arg.timeText }}</span>
                    <i>{{ arg.event.title }}</i>
                    <i>{{ arg.event.title }}</i>
                </div>
            </el-popover>
        </template>
    </FullCalendar>
    
</template>

<script>
//此处只显示新增的部分
export default {
	methods: {
         handleMore() {},
	}
}
</script>
```

思路二：自己写，监听鼠标事件，通过定位的方式实现。这个仅供参考思路哈~

```
<template>
<!-- 自定义头部的代码省略 -->

<!-- popper弹窗 -->
<div id="popper"
     ref="popper"
     class="popper"
     @mouseout="handleMouseOut"
     @mouseover="handleMousover">
    <p>{{ currentView && currentView.title }}</p>
    <button @click="handleMore">
        更多配置
    </button>
</div>

 <FullCalendar
    id="calendar"
    ref="fullCalendar"
    class="demo-app-calendar"
    :options="calendarOptions">
    <template v-slot:eventContent="arg">
        <div class="popper-content">
            <span>{{ arg.timeText }}</span>
            <i>{{ arg.event.title }}</i>
        </div>
    </template>
</FullCalendar>
</template>

<script>
let timer = null;
export default {
	data() {
		return {
		    currentView: {},
		    calendarOptions: {
                ....
                eventMouseEnter: this.eventMouseEnter,
                eventMouseLeave: this.handleMouseOut,
		    }
		}
	},
	methods: {
		// 事件的鼠标hover事件
        eventMouseEnter(e) {
            const pos = e?.jsEvent;
            const { popper } = this.$refs;
            popper.style.display = 'block';
            popper.style.left = `${pos.x - popper.offsetWidth / 2}px`;
            popper.style.top = `${pos.y - popper.offsetHeight}px`;
            this.currentView = {
                title: e.event.title,
                id: e.event.id,
            };
        },
        // 弹窗的鼠标hover事件
        handleMousover(e) {
            clearTimeout(timer);
            const { popper } = this.$refs;
            popper.style.display = 'block';
        },
         // 弹窗的鼠标out事件
        handleMouseOut() {
            timer = setTimeout(() => {
                const { popper } = this.$refs;
                popper.style.display = 'none';
                this.currentView = {};
            }, 0);
        },
        // 更多配置操作
        handleMore() {
            console.log(this.currentView.id, '===================');
        },
	}
}
</script>

<style>
.popper {
    position: fixed;
    width: 200px;
    height: 140px;
    padding: 20px;
    border: 1px solid #eee;
    text-align: center;
    box-shadow: 0 2px 4px 0 rgba(0, 0, 0, 0.1);
    z-index: 1000;
    display: none;
    background-color: rgb(188, 236, 250);
}
</style>
```



**功能点3：新增事件**

```
<template>
<!-- 在自定义头部的地方、加上新增事件的按钮 -->

<div class="fc-toolbar">
        <div class="fc-left">
            <el-button-group>
                <el-button @click="month">
                    月
                </el-button>
                <el-button @click="week">
                    周
                </el-button>
                <el-button @click="today">
                    今天
                </el-button>
            </el-button-group>
        </div>
        <div class="fc-center">
            <el-button icon="el-icon-arrow-left"
                       @click="prev" />
            <h2>
                {{ title }}
            </h2>
            <el-button icon="el-icon-arrow-right"
                       @click="next" />
        </div>
        <div>
            <el-button class="search"
                       style="marginRight: 20px"
                       type="button"
                       @click="addEvent">
                新增
            </el-button>
            <el-button class="search"
                       type="button"
                       @click="search">
                查询
            </el-button>
        </div>
    </div>
</template>

<script>
	methods: {
		// 在日历中增加事件，大伙可以根据需求去增加自定义时间的事件，我这边是写死一个日期的事件用来示范。
        addEvent() {
        	// 第一种思路： 使用官方提供的方法
            // this.calendarApi.addEvent({
            //    title: 'event 11',
            //    start: '2021-11-28',
            //})
          
            //第二种：通过vue的双向绑定
            this.calendarOptions.events.push({
                title: 'event 11',
                start: '2021-11-28',
            });
        },
	}
</script>
```



**功能点四：修改周视图上面的日期**

 默认显示是 星期一 11/19, 需要自定义的话，可以在views属性中自定义个 周试图来自定义 头部的日期。

```
views: {
    customeGridWeek: {
        type: 'timeGridWeek',
        dayHeaderFormat: {
            weekday: 'long',
        },
    },
},

//当然也可以直接在calendarOptions属性设置 
dayHeaderFormat: {
    weekday: 'long',
},
但这个就是争对全部视图了，如果只是争对周试图进行修改的话，建议自定义修改周视图
```

![周视图](E:\周视图.png)



下面resourceTimeLine的时间线展示，我自己刚开始搞的时候，一直显示不出来对应的日历。报没有安装相应的插件，但其实已经引入了。去网上搜也没有搜到对应的解决方法。然后我看网上可以的朋友，我就直接把我的版本绑定到对应的 5.5.0。然后重新安装依赖之后，就神奇般的可以正常显示了，所以不清楚最新版是不是做了某些改动，需要额外配置什么东西。



**resourceTimeLine的日历展示**

我们要改造我们的自定义头部，增加一个下拉选择，手动切换对应的 timeline 视图。

**想要修改默认的时间线显示，最好不要全局设置，除非你想要视图的时间线显示是全部一样的。最好的方式是通过自定义views, 修改时间线上显示的时间。**

![修改时间线显示](E:\修改时间线显示.png)

```
<template>
	<div>
		<div class="fc-toolbar">
            <div class="fc-left">
                <el-button-group>
                    <el-button @click="month">
                        月
                    </el-button>
                    <el-button @click="week">
                        周
                    </el-button>
                    <el-button @click="today">
                        今天
                    </el-button>
                </el-button-group>
            </div>
            <div class="fc-center">
                <el-button icon="el-icon-arrow-left"
                           @click="prev" />
                <h2>
                    {{ title }}
                </h2>
                <el-button icon="el-icon-arrow-right"
                           @click="next" />
            </div>
            <div>
                <el-select v-model="type"
                           style="marginRight: 20px"
                           @change="handleType">
                    <el-option
                        v-for="item in options"
                        :key="item.value"
                        :label="item.label"
                        :value="item.value" />
                </el-select>
                <el-button class="search"
                           style="marginRight: 20px"
                           type="button"
                           @click="addEvent">
                    新增
                </el-button>
                <el-button class="search"
                           type="button"
                           @click="search">
                    查询
                </el-button>
            </div>
        </div>
	</div>
	
	<!-- 下面的不变，沿用上面的 -->
<template>


<script>
//需增加的插件
import resourceTimelinePlugin from '@fullcalendar/resource-timeline';
export default {
    components: {
        FullCalendar, // make the <FullCalendar> tag available
    },
    data() {
    	title: '',
        currentView: {},
        options: [
            { value: 'timeline', label: 'resource-timeline' },
            { value: 'dategrid', label: 'agenda' },
        ],
        type: 'dategrid',
        calendarOptions: {
            locale: 'zh',
            timeZone: "UTC",
            plugins: [dayGridPlugin, timeGridPlugin, listPlugin, resourceTimelinePlugin, interactionPlugin],
            initialView: 'dayGridMonth',
            resourceAreaWidth: 200,
            contentHeight: 600,
            slotMinWidth: 70,
            resourceOrder: 'number',
            editable: true,
            dayMaxEvents: true, // allow "more" link when too many events
            eventDurationEditable: true, // 可以调整事件的时间
            selectable: true, // 日历格子可选择
            nowIndicator: true, // 现在的时间线显示
            eventDisplay: 'block', // 争对全天的情况下，以块状显示
            headerToolbar: false, // 隐藏头部的导航栏
            selectMirror: false,
            displayEventEnd: true, // like 08:00 - 13:00
            eventTimeFormat: { // like '14:30:00'
                hour: '2-digit',
                minute: '2-digit',
                meridiem: false,
                hour12: false, // 设置时间为24小时
            },
            slotLabelFormat: { // 左侧时间格式
                hour: '2-digit',
                minute: '2-digit',
                meridiem: 'lowercase',
                hour12: false, // false设置时间为24小时
            },
            events: [],
            eventColor: '#378006',
            allDayText: '全天',
            dateClick: this.handleDateClick,
            resourceAreaHeaderContent: "Rooms",  // 左侧resource的header显示文本，默认是resources
            resources: [  // 对应左侧resource的数据，几条就代表几行
                {
                    id: 'number_1',
                    title: 'asas',
                    number: 1
                },
                {
                    id: 'number_2',
                    title: 'asas',
                    number: 2
                },
                {
                    id: 'number_3',
                    title: 'asas',
                    number: 3
                },
            ],
            schedulerLicenseKey: 'GPL-My-Project-Is-Open-Source', // 目前我也不清楚为啥需要这个凭证，值是从官网粘贴过来的
            resourceLabelContent(arg) {  // 自定义左侧resource值的显示
                return {
                    html: `<div>id: ${arg.resource.id}</div><div>title: ${arg.resource.title}</div>`
                }
            },
            views: {  // 自定义 resourceTimeLine的 时间显示
                  customTimeLineWeek: {
                        type: 'resourceTimeline',
                        duration: { weeks: 1 },
                        slotDuration: { days: 1 },
                        buttonText: 'Custom Week',
                        slotLabelFormat: {
                            weekday: 'long',
                            // month: 'numeric',
                            // day: 'numeric',
                            omitCommas: true,
                        },
                    },
                    customTimeLineMonth: {
                        type: 'resourceTimeline',
                        duration: { month: 1 },
                        slotLabelFormat: {
                            // month: 'numeric',
                            day: 'numeric',
                            // omitCommas: true,
                        },
                    },
                    customGridWeek: {
                        type: 'timeGridWeek',
                        dayHeaderFormat: {
                            weekday: 'long',
                        },
                        slotLabelFormat: {
                            // 左侧时间格式
                            hour: '2-digit',
                            minute: '2-digit',
                            meridiem: 'lowercase',
                            hour12: false, // false设置时间为24小时
                        },
                    },
            },
            // 切换视图调用的方法
            datesSet(arg) {},
        },
        calendarApi: null,
        monthEvent: [
                {
                    id: 'number_1',
                    resourceId: 'number_1',
                    title: 'event 1',
                    start: '2021-10-21',
                    color: 'purple',
                },
                {
                    resourceId: 'number_2',
                    id: 'number_2',
                    title: 'event 2',
                    start: '2021-10-22',
                    color: 'purple',
                },
                { title: 'event 3', start: '2021-10-23' },
                { title: 'event 4', start: '2021-10-24' },
                { title: 'event 5', start: '2021-10-21' },
                { title: 'event 6', start: '2021-10-21', color: 'purple' },
                { title: 'event 7', start: '2021-10-21' },
                {
                    id: 'number_3',
                    resourceId: 'number_3',
                    title: 'event 6',
                    start: '20211120',
                    end: '20211122',
                    color: 'purple',
                    extendedProps: {
                        description: 'asdasdasdasdasdasdasdasds',
                    },
                },
                {
                    id: 4,
                    title: 'event 7',
                    start: '2021-11-22',
                    extendedProps: {
                        description: '444444444444',
                    },
                },
        ],
        weekEvent: [
            {
                id: 'number_1',
                resourceId: 'number_1',
                title: 'week_event',
                start: '2021-11-11',
                color: 'purple',
            },
        ],
    }，
    methods: {
    	 getDtata() {
            setTimeout(() => {
                this.calendarOptions.events =
                    this.calendarApi.view?.type === 'dayGridMonth' ? this.monthEvent : this.weekEvent;
            }, 200);
        },
        more() {},
        // 增加事件
        addEvent() {
            this.calendarOptions.events.push({
                id: 'number_4',
                resourceId: 'number_3', // 如果是resourceTimeLine视图的话，增加事件时，这个resourceId一定要加上，对应的值为 你要在哪个resource里面加事件，就是用哪个resource的对应id. 通过这个resourceId 去将当前事件绑定到上面。不加这个属性的话，是不会显示出来的。
                title: 'event 11',
                start: '2021-11-28',
            });
        },
        // 单击事件
        handleDateClick(e) {
            if (e.dateStr !== prev) {
                clickCount = 0;
            }
            clickCount += 1;
            prev = e.dateStr;
            setTimeout(() => {
                if (clickCount === 2) {
                    console.log('db click');
                } else if (clickCount === 1) {
                    console.log('one click');
                }
                clickCount = 0;
            }, 300);
        },
        prev() {
            this.calendarApi.prev();
            this.title = this.calendarApi.view?.title;
        },
        next() {
            this.calendarApi.next();
            this.title = this.calendarApi.view?.title;
        },
        today() {
            this.calendarApi.today();
            this.title = this.calendarApi.view?.title;
        },
        month() {
            if (this.type === 'timeline') {
                this.calendarApi.changeView('customTimeLineMonth');
            }else {
                this.calendarApi.changeView('dayGridMonth');
            }
            this.calendarApi.today();
            this.title = this.calendarApi.view?.title;
        },
        week() {
            if (this.type === 'timeline') {
                this.calendarApi.changeView('customTimeLineMonth');
            }else {
                this.calendarApi.changeView('customGridWeek');
            }
            this.calendarApi.today();
            this.title = this.calendarApi.view?.title;
        },
        day() {
            this.calendarApi.today();
            this.title = this.calendarApi.view?.title;
        },
        search() {
            this.calendarApi.changeView('dayGrid', {
                start: '2021-10-21',
                end: '2021-10-23',
            });
        },
        // 下拉框选择的change事件
        handleType() {
            if(this.type ==='timeline') {
                this.calendarApi.changeView('customTimeLineMonth')
                this.calendarOptions.slotLabelFormat = null // 设置null 是因为我们全局设置了 slotLabelFormat的属性，是针对dayGrid视图的，不适用于timeline视图。
            } else {
                this.calendarApi.changeView('dayGridMonth')
            }
        },
    }
}

<script>
```



**需要注意的点：**

**1、为啥增加了事件没有正常显示在视图上？**

在resourceTimeLine的视图下，定义事件、增加事件的时候，事件对象务必要增加一个属性 resourceId，对应的值为 你要在哪个resource里面加事件，就是用哪个resource的对应id. 通过这个resourceId 去将当前事件绑定到上面。



