<template>
    <div class="calendar">
        <input
            :value="dateString"
            readonly
            @click="toggleCalendar"
            class="calendar__input"
        />

        <div  aria-modal="true" v-show="showCalendar">
            <div class="calendar__line">
                <button class="calendar__button" @click="decrementYear()"><<</button>
                <button class="calendar__button" @click="decrementMonth()"><</button>
                <div class="calendar__mount-title">{{monthNames[bufferDate.getMonth()] + bufferDate.getFullYear()}}</div>
                <button class="calendar__button" @click="incrementMonth()">></button>
                <button class="calendar__button" @click="incrementYear()">>></button>
            </div>
            <div class="calendar__line">
                <div class="calendar__button" v-for="day in dayNames">{{day}}</div>
            </div>
            <div v-for="week in datesMatrix" class="week">
                <button v-for="date in week"
                        @click="pickDate(date.date)"
                        class="calendar__button"
                        :class="{'calendar__button--disabled': ! date.enabled}"
                >
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