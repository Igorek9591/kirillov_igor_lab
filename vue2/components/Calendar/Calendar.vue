<template>
    <div class="calendar">
        <input
            :value="dateString"
            readonly
            @click="toggleCalendar"
        />

        <div  aria-modal="true" v-show="showCalendar" class="calendar__selector">
            <div class="calendar__line">
                <div class="calendar__button" @click="changeBufferedDate(-1)">«</div>
                <div class="calendar__button" @click="changeBufferedDate(0,-1)">‹</div>
                <div class="calendar__mount-title">
                    {{monthNames[bufferDate.getMonth()] + bufferDate.getFullYear()}}
                </div>
                <div class="calendar__button" @click="changeBufferedDate(0, 1)">›</div>
                <div class="calendar__button" @click="changeBufferedDate(1)">»</div>
            </div>
            <div class="calendar__line">
                <div v-for="day in dayNames" class="calendar__button">{{day}}</div>
            </div>
            <div v-for="week in datesMatrix" class="calendar__line">
                <div
                    v-for="date in week"
                    @click="pickDate(date.date)"
                    class="calendar__button"
                    :class="{'calendar__button--disabled': ! date.enabled}"
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
        monthNames() {
            return [
                "янв ", "фев ", "апр ", "мар ", "май ", "июн ", "июл ", "авг ", "сен ", "окт ", "ноя ", "дек ",]
        },
        dayNames(){
            return ['пн', 'вт', 'ср', 'чт', 'пт', 'сб', 'вс',]
        },
        datesMatrix() {
            let date = new Date(this.bufferDate)
            let next_month = (date.getMonth() + 1) % 12
            date.setDate(1)
            date.setDate(date.getDate() - date.getDay())

            let enabled = false
            let matrix = []
            while (date.getMonth() !== next_month) {
                let arr = []
                for (let i = 0; i < 7; i++) {
                    if (date.getDate() === 1)
                        enabled = date.getMonth() !== next_month
                    arr.push({date: date.getDate(), enabled: enabled})
                    date.setDate(date.getDate() + 1)
                }
                matrix.push(arr)
            }
            return matrix
        },
        dateString() {
            return `${this.selectedDate.getFullYear()}-${this.selectedDate.getMonth() + 1}-${this.selectedDate.getDate()}`
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
            if(this.showCalendar)
                this.closeCalendar()
            else
                this.openCalendar()
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