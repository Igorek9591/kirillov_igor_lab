<template>
    <div
        :tabindex="0"
        @focus="openCalendar"
        @blur="closeCalendar"
    >
        <input :value="selectedDate.toISOString().slice(0, 10)"/>

        <div  aria-modal="true" class="calendar" v-show="showCalendar">
            <div class="week">
                <button class="day" v-on:click="decrementYear()"><<</button>
                <button class="day" v-on:click="decrementMonth()"><</button>
                <div style="width: 90px; text-align: center">{{monthNames[bufferDate.getMonth()] + bufferDate.getFullYear()}}</div>
                <button class="day" v-on:click="incrementMonth()">></button>
                <button class="day" v-on:click="incrementYear()">>></button>
            </div>
            <div class="week">
                <div class="day" v-for="day in dayNames">{{day}}</div>
            </div>
            <div v-for="week in datesMatrix" class="week">
                <button v-for="date in week"
                        v-on:click="clickDate(date.date, date.enabled)"
                        class="day">
                    {{date.date}}
                </button>
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
            matrixTrigger: false,
            showCalendar: false,
        }
    },
    computed: {
        monthNames() {
            return [
                "Янв ", "Фев ", "Апр ", "Мар ", "Май ", "Июн ", "Июл ", "Авг ", "Сен ", "Окт ", "Ноя ", "Дек ",]
        },
        dayNames(){
            return ['Пн', 'Вт','Ср','Чт','Пт','Сб','Вс',]
        },
        datesMatrix() {
            this.matrixTrigger
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
    },

    methods: {
        openCalendar() {
            this.bufferDate = new Date(this.selectedDate)
            this.showCalendar = true
        },
        closeCalendar() {
            this.showCalendar = false
        },
        clickDate(date, enabled) {
            if (enabled){
                this.selectedDate = new Date(this.bufferDate)
                this.selectedDate.setDate(date)
                this.closeCalendar()
            }
        },
        incrementMonth(){
            this.bufferDate.setMonth(this.bufferDate.getMonth() + 1)
            this.matrixTrigger = !this.matrixTrigger
        },
        decrementMonth(){
            this.bufferDate.setMonth(this.bufferDate.getMonth() - 1)
            this.matrixTrigger = !this.matrixTrigger
        },
        incrementYear(){
            this.bufferDate.setFullYear(this.bufferDate.getFullYear() + 1)
            this.matrixTrigger = !this.matrixTrigger
        },
        decrementYear(){
            this.bufferDate.setFullYear(this.bufferDate.getFullYear() - 1)
            this.matrixTrigger = !this.matrixTrigger
        },
    }
}
</script>

<style scoped lang="less">
@import "styles/styles.less";
</style>