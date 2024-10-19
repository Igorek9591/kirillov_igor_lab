<template>
    <div class="calendar">
        <input
            :value="dateString"
            readonly
            @click="toggleCalendar"
        />
        <div  aria-modal="true" v-show="showCalendar" class="calendar__selector">
            <div class="calendar__upper">
                <div class="calendar__button" @click="changeBufferedDate(-1)">«</div>
                <div class="calendar__button" @click="changeBufferedDate(0,-1)">‹</div>
                <div class="calendar__mount-title">{{MonthTitle}}</div>
                <div class="calendar__button" @click="changeBufferedDate(0, 1)">›</div>
                <div class="calendar__button" @click="changeBufferedDate(1)">»</div>
            </div>
            <div class="calendar__grid">
                <div v-for="day in dayNames" class="calendar__button">{{day}}</div>
                <div
                    v-for="(date, index) in datesList"
                    @click="pickDate(date.date)"
                    :key="index"
                    :class="{'calendar__button--disabled': ! date.enabled}"
                    class="calendar__button"
                >
                    {{date.date}}
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: "Calendar",

    data() {
        return {
            selectedDate: new Date(),
            bufferDate: new Date(),
            showCalendar: false,
        }
    },
    computed: {
        dayNames(){
            return ['пн', 'вт', 'ср', 'чт', 'пт', 'сб', 'вс',]
        },
        MonthTitle(){
            const monthNames = ["янв ", "фев ", "апр ", "мар ", "май ", "июн ", "июл ", "авг ", "сен ", "окт ", "ноя ", "дек ",]
            return monthNames[this.bufferDate.getMonth()] + this.bufferDate.getFullYear()
        },
        datesList() {
            const date = new Date(this.bufferDate)
            const next_month = (date.getMonth() + 1) % 12
            date.setDate(1)
            date.setDate(date.getDate() - date.getDay())

            let enabled = false
            const dates = []
            while (date.getMonth() !== next_month) {
                for (let i = 0; i < 7; i++) {
                    if (date.getDate() === 1) {
                        enabled = date.getMonth() !== next_month
                    }
                    dates.push({date: date.getDate(), enabled: enabled})
                    date.setDate(date.getDate() + 1)
                }
            }
            return dates
        },
        dateString() {
            return `${this.selectedDate.getFullYear()}-${String(this.selectedDate.getMonth() + 1).padStart(2, '0')}-${String(this.selectedDate.getDate()).padStart(2, '0')}`
        }
    },

    methods: {
        openCalendar() {
            this.bufferDate = new Date(this.selectedDate)
            this.showCalendar = true
        },
        closeCalendar() {
            this.showCalendar = false
        },
        toggleCalendar()  {
            if(this.showCalendar) {
                this.closeCalendar()
            }
            else {
                this.openCalendar()
            }
        },
        pickDate(date) {
            this.selectedDate = new Date(this.bufferDate)
            this.selectedDate.setDate(date)
            this.closeCalendar()
        },
        changeBufferedDate(year=0, month=0){
            let newDate = new Date(this.bufferDate)
            newDate.setFullYear(newDate.getFullYear() + year)
            newDate.setMonth(newDate.getMonth() + month)
            this.bufferDate = newDate
        },
    }
}
</script>

<style scoped lang="less">
    @import "styles/styles.less";
</style>
