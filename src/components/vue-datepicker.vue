<template>

    <div class="lpdp">

        <div class="overlay" @click="show = false" v-if="show"></div>

        <!--Input Field-->
        <div class="input-group input-group-lg" :class="{ 'has-error' : validError }">

            <div class="input-group-btn">
                <span class="btn btn-primary" @click="action = null, show = true">
                    <i class="ion-android-calendar"></i>
                </span>
            </div>

            <input class="form-control" :readonly="readonly" :required="required" :name="name"
                   :placeholder="placeholder" v-model="input">

            <div class="input-group-btn">
                <span class="btn btn-default" @click="clearInput">
                    <i class="ion-android-delete"></i>
                </span>
            </div>
        </div>

        <p v-if="validError" class="text-danger" v-text="validError">Invalid Date</p>

        <transition name="animate-panel" appear mode="out-in">

            <div class="datepicker" v-if="show">

                <div class="picker__head" v-text="selectedText"></div>

                <div class="picker__body clearfix">

                    <!--Calender-->
                    <div class="calender text-center" v-if="action == null || action == 'selectDate'" v-touch="{ left: nextMonth, right : previousMonth }">

                        <div class="context__actions">
                            <span class="action left" @click="previousMonth"><i class="ion-arrow-left-a"></i></span>
                            <span class="action" v-text="months[context.months]" @click="action = 'selectMonth'"></span>
                            <span class="action" v-text="context.years" @click="action = 'selectYear'"></span>
                            <span class="action right" @click="nextMonth"><i class="ion-arrow-right-a"></i></span>
                        </div>

                        <span v-for="dayName in week" v-text="dayName" class="action"></span>

                        <template v-for="week in calender">
                            <span
                                    v-for="day in week"
                                    v-text="day.object.date"
                                    class="action"
                                    :class="dayClasses(day)"
                                    @click="day.isCurrentMonth && day.isSelectable ? selectDate(day.object) : ''"
                            ></span>
                        </template>

                    </div>

                    <!--Month-->
                    <div class="months" v-else-if="action == 'selectMonth'">

                        <template v-for="(month, index) in months" class="month">
                            <transition name="list-item" appear mode="out-in">
                                <span
                                    v-text="month"
                                    class="action month"
                                    :class="monthClasses(month, index)"
                                    @click="selectMonth(index)"
                                    :disabled="! monthIsSelectable(index)"
                                ></span>
                            </transition>
                        </template>

                    </div>

                    <!--Years-->
                    <div class="years text-center clearfix" v-if="action == 'selectYear'" v-touch="{ left: forwardYears, right : rewindYears }">

                        <div class="context__actions">
                            <span class="action left" @click="rewindYears"><i class="ion-arrow-left-a"></i></span>
                            <span class="action" v-text="context.years" @click="action = null"></span>
                            <span class="action right" @click="forwardYears"><i class="ion-arrow-right-a"></i></span>
                        </div>

                        <template v-for="year in yearsRange" >
                            <transition name="list-item">
                                <span
                                        class="action year"
                                        :key="year"
                                        v-text="year"
                                        @click="selectYear(year)"
                                        :class="yearClasses(year)"
                                ></span>
                            </transition>
                        </template>

                    </div>

                    <!--Time-->
                    <div class="time clearfix" v-if="action == 'selectTime' && time">

                        <!--Hours-->
                        <div class="hrs">
                            <h5>Hours</h5>
                            <div class="input-group">
                                <div class="input-group-btn">
                                    <span class="btn btn-default" @click="! dateAndTime.hours <= 0 ? --dateAndTime.hours : 0">
                                        <i class="ion-minus-round"></i>
                                    </span>
                                </div>
                                <select class="form-control" v-model="dateAndTime.hours">
                                    <option v-for="n in hours" v-text="n"></option>
                                </select>
                                <div class="input-group-btn">
                                    <span class="btn btn-default" @click="dateAndTime.hours < 23 ? ++dateAndTime.hours : ''">
                                        <i class="ion-plus-round"></i>
                                    </span>
                                </div>
                            </div>

                        </div>

                        <!--minutes-->
                        <div class="mins">
                            <h5>Minutes</h5>
                            <div class="input-group">
                                <div class="input-group-btn">
                                    <span class="btn btn-default" @click="! dateAndTime.minutes <= 0 ? --dateAndTime.minutes : ''">
                                        <i class="ion-minus-round"></i>
                                    </span>
                                </div>
                                <select class="form-control" v-model="dateAndTime.minutes">
                                    <option v-for="n in minutes" v-text="n"></option>
                                </select>
                                <div class="input-group-btn">
                                    <span class="btn btn-default" @click="dateAndTime.minutes < 59 ? ++dateAndTime.minutes : ''">
                                        <i class="ion-plus-round"></i>
                                    </span>
                                </div>
                            </div>
                        </div>

                    </div>

                </div>

                <div class="picker__footer">

                    <span class="action" @click="action = 'selectDate'"><i class="ion-calendar"></i></span>
                    <span class="action" v-if="time && dateAndTime.date" @click="action = 'selectTime'"><i class="ion-clock"></i></span>

                    <span class="action right" @click="show = false"><i class="ion-close-round"></i></span>
                    <span class="action right" @click="assignDates(context, today), action = null"><i class="ion-android-calendar"></i></span>
                    <span class="action right" @click="assignDates(context, dateAndTime), action = null" v-if="this.dateAndTime.date"><i class="ion-android-checkmark-circle"></i></span>

                </div>

            </div>

        </transition>

    </div>

</template>

<script>
    import moment from 'moment';

    import Touch from '../directives/touch'

    export default {
        directives : {
            Touch
        },
        props : {
            format: {
                type: String,
                default: 'YYYY-MM-DD HH:mm:ss'
            },
            min : {
                type: String,
                default: null
            },
            max: {
                type: String,
                default: null
            },
            time: {
                type: Boolean,
                default: true
            },
            readonly: {
                type: Boolean,
                default: false
            },
            value: {
                type: String
            },
            name: {
                type: String,
                default: 'Laravel Plus Datepicker'
            },
            placeholder: {
                type: String,
                default: 'Pick a date...'
            },
            required: {
                type: Boolean,
                default: false
            },
            validate: {
                type: Boolean,
                default: false
            }
        },
        data() {
            return {
                show: false,
                week: moment.weekdaysMin(),
                today: moment().toObject(),
                context: moment().toObject(),
                timeType: 24,
                action: null,
                months: moment.months(),
                range: {
                    min : this.min ? moment(this.min) : null,
                    max: this.max ? moment(this.max) : null
                },
                years : {
                    min: 100, // years backward
                    max: 100, // years forward
                    showCount: 25 // display count
                },
                dateAndTime: {},
                validError: null,
                input: null
            }
        },
        created() {
            this.setDateAndTime();
        },
        methods: {
            setDateAndTime: function () {
                if ( this.value ) {
                    let m = moment(this.value);
                    this.context = this.dateAndTime = Object.assign({}, this.dateAndTime, m.toObject());
                    this.input = m.format(this.format);
                }
            },

            previousMonth: function () {

                let min = this.range.min ;

                if( min && min.isSame(this.context, 'month') ) return;

                this.context = moment(this.context).subtract(1, 'months').toObject();
            },

            nextMonth: function () {

                let max = this.range.max;

                if( max && max.isSame(this.context, 'month') ) return;

                this.context = moment(this.context).add(1, 'months').toObject();

            },

            rewindYears: function () {

                let startFrom = this.context.years - this.years.showCount;

                let min = this.range.min ? this.range.min.year() : (this.today.years - this.years.min);

                if( startFrom < min ) {
                    this.context.years = min;
                } else {
                    this.context.years = this.context.years - this.years.showCount;
                }

            },

            forwardYears: function () {

                let end = this.context.years + (this.years.showCount  - 1);

                let max = this.range.max ? this.range.max.year() : (this.today.years + this.years.max);

                if( (max - end) < this.years.showCount ) {
                    // small
                    this.context.years = max - (this.years.showCount - 1);

                } else {

                    this.context.years = this.context.years + this.years.showCount;

                }
            },

            selectDate: function (date) {

                let obj = {
                    years : date.years,
                    months : date.months,
                    date : date.date,
                    hours : this.dateAndTime.hours ? this.dateAndTime.hours : date.hours,
                    minutes : this.dateAndTime.minutes ? this.dateAndTime.minutes : date.minutes,
                }

                this.dateAndTime = Object.assign({}, this.dateAndTime, obj);

                this.input = moment(this.dateAndTime).format(this.format);
            },

            selectMonth: function (month) {
                this.context.months = month;
                this.action = null;
            },

            selectYear: function (year) {

                this.context.years = year;

                if( this.range.min && this.range.min.isSame(this.context, 'year') ) {
                    ! this.isBetweenRange(this.context , 'month') ? this.context.months =  this.range.min.month() : '';
                }

                if( this.range.max && this.range.max.isSame(this.context, 'year') ) {
                    ! this.isBetweenRange(this.context , 'month') ? this.context.months = this.range.max.month() : '';
                }

                this.dateAndTime.years = year;

                this.action = null;
            },

            isBetweenRange: function (test, type) {
                return moment(test).isBetween(this.range.min, this.range.max, type, '[]');
            },

            monthIsSelectable: function (month) {
                let m = moment(this.context).set('month', month);
                return moment(m).isBetween(this.range.min, this.range.max, 'month', '[]');
            },

            monthClasses: function (month, index) {
                return {
                    'selected' : this.dateAndTime.months == index,
                    //'context' : this.context.months == index && this.dateAndTime.months != index,
                    'today' : this.today.months == index
                }
            },

            yearClasses: function (year) {
                return {
                    'selected' : this.dateAndTime.years == year,
                    //'context' : this.context.years == year && this.dateAndTime.years != year,
                    'b' : this.today.years == year
                }
            },

            dayClasses : function (day) {
                return {
                    'today' : day.isToday,
                    'selected' : day.isSelected,
                    'dim' : ! day.isSelectable || ! day.isCurrentMonth,
                    'selectable' : day.isSelectable && day.isCurrentMonth
                }
            },

            assignDates: function (context, value) {
                context.years = value.years;
                context.months = value.months;
                context.date = value.date;
            },

            clearInput: function () {
                this.dateAndTime = {};
                this.input = null;
                this.validateInput();
            },

            validateInput: function () {
                if(!  this.validate ) return;

                this.validError = null;

                let date = moment(this.input);

                if( ! this.input || ! date.isValid() ) {
                    return this.setError('Select valid date');
                }

                if( this.range.min || this.range.max )  {

                    if ( ! this.isBetweenRange(date, 'day') ) {
                        return this.setError('Date should be in specific range');
                    }

                }

            },

            setError: function (msg) {
                this.validError = msg;

                if( this.range.min || this.range.max )  {

                    this.context = this.isBetweenRange(this.today, 'day')
                        ? Object.assign({}, this.context, moment(this.today).toObject())
                        : Object.assign({}, this.context, moment(this.range.min).toObject());

                }

                this.dateAndTime = {};
            }

        },

        computed: {

            calender: function () {

                let calender = [];

                let iteratorDate = moment(this.context).startOf('month').startOf('week');

                for (let weekCount = 0; weekCount < 6; weekCount++) {

                    let weeks = [];

                    for (let day = 0; day < 7; day++) {

                        let iterator = moment(iteratorDate);

                        let selectable = this.range.min ? iterator.isSameOrAfter(this.range.min, 'day') : true;

                        selectable = selectable && this.range.max ? ! iterator.isSameOrAfter(this.range.max, 'day') : selectable;

                        weeks.push({
                            isCurrentMonth: iterator.isSame(this.context, 'month'),
                            isSelected: this.dateAndTime.date && iterator.isSame(this.context, 'month') ? iterator.isSame(this.dateAndTime, 'day') : false,
                            isSelectable: selectable,
                            isToday: iterator.isSame(this.today, 'day'),
                            object: iterator.toObject()
                        });

                        iteratorDate.add(1, 'day');
                    }

                    calender.push(weeks);
                }

                return calender;
            },

            inputValue: function () {

                return this.dateAndTime.date ? moment(this.dateAndTime).local().format(this.format) : '';

            },

            selectedText: function () {
                let format = this.time ? 'ddd, MMM-D-YYYY @ h:mm a' : 'ddd, MMM-D-YYYY';

                let text = this.time ? 'Select Date and Time...' : 'Select Date...';

                text = this.dateAndTime.date ? moment(this.dateAndTime).format(format) : text ;

                return ! this.dateAndTime.date && this.dateAndTime.hours ? 'Select date also...' : text;
            },

            yearsRange: function () {

                let showCount = this.years.showCount;

                let max = this.range.max ? this.range.max.year()  : this.today.years + this.years.max;

                showCount = (max - this.context.years) < this.years.showCount ? max - (this.context.years - 1) : showCount ;

                return Array( showCount ).fill(0).map( ( e , i ) => {
                    return i + this.context.years;
                } );

            },

            hours: function () {
                return Array.from(Array(24).keys());
            },

            minutes: function () {
                return Array.from(Array(60).keys());
            },
        },

        watch: {
            input: function (input) {
                this.validateInput();
            }
        }
    }
</script>

<style lang="scss">
    $primary-color : #41B883;
    $primary-text-color : #ffffff;

    .lpdp {
        position: relative;
        background: white;
        z-index: 9999;
        width: 100%;

        .overlay {
            position: fixed;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: rgba(0,0,0, 0.5);
            z-index: 999;
        }

        .datepicker {
            position: absolute;
            background: white;
            top: 0;
            border-color: $primary-color;
            z-index: 999;
            width: 100%;
            max-width: 400px;
            border-radius: 0;
            box-shadow: 0 14px 28px rgba(0, 0, 0, 0.25), 0 10px 10px rgba(0, 0, 0, 0.22);
        }

        .picker__head {
            background: $primary-color;
            color: $primary-text-color;
            font-size: 20px;
            padding: 10px;
        }

        .picker__body {
            display: block;
            padding: 10px;
        }

        .picker__footer {
            display: block;
            padding: 10px 5px 10px 5px;
            border-top: 1px solid #dcdcdc;

            span.action {
                font-size: 16px;
                padding: 3px 10px;

                &:hover, &:focus {
                    background: inherit;
                    color: $primary-color;
                }

            }
        }

        .action {
            font-size: 18px;
            line-height: 20px;
            padding: 0 5px;
            display: inline-block;

            &:hover, &:focus {
                cursor: pointer;
            }

            &.b {
                font-weight: bold;
            }
        }

        .datepicker span {
            transition: all 200ms;

            &:hover, &:focus {
                color: $primary-color;
            }

            &.dim {
                color: #c9c9c9;

                &:hover, &:focus {
                    cursor: not-allowed;
                }
            }

            &.selectable:not(.selected) {
                &:hover, &:focus {
                    color: $primary-color;
                }
            }

            &.selected {
                background: $primary-color;
                color: $primary-text-color;
            }

            &.context {
                color: $primary-color;
            }

            &.today {
                font-weight: bold;
            }

            &.left {
                float: left !important;
            }

            &.right {
                float: right !important;
            }
        }

        .calender {

            span {
                float: left;
                width: 14.2857142857%;
                padding: 6px 0;
            }

        }

        .context__actions {
            padding: 0 0 5px 0;
            margin-bottom: 10px;
            border-bottom: 1px solid #dcdcdc;

            span {
                padding: 0 10px;
                width: auto;
                float: none;

                &:hover, &:focus {
                    background: inherit;
                    color: inherit;
                }
            }
        }

        .action {

            &.month, &.year {
                padding: 5px 5px;
                margin: 10px 0;
                width: 25%;
                text-align: center;
            }

            &.year {
                width: 20%;
            }
        }

        .time {
            display: block;
            width: 100%;
            padding: 20px;
        }

        .hrs, .mins {
            display: inline-block;
            width: 45%;
            text-align: center;

            h5 {
                margin-top: 0;
            }
        }

        .mins {
            float: right;
        }

        // Animations
        .list-item-enter-active {
            transition: all 200ms;
        }
        .list-item-leave-active {
            display: none;
        }
        .list-item-enter, .list-item-leave-to {
            opacity: 0;
            transform: scale(0.5);
        }

        // Picker Animation
        .animate-panel-enter-active {
            transform-origin: 0% 0%;
            transition: all 100ms;
        }

        .animate-panel-leave-active {
            display: none;
        }

        .animate-panel-enter, .animate-panel-leave-to {
            transform: scale(0.8);
        }
    }

</style>