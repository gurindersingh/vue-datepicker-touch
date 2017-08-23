<template>
    <div id="app">

        <div class="container">

            <!--Logo-->
            <div class="row">

                <!--Logo-->
                <div class="col-lg-12 text-center">
                    <img src="/static/assets/logo.svg" class="logo">
                </div>

                <div class="col-lg-12 text-center">
                    <h1>Laravel Plus Vuejs Datepicker</h1>
                    <h3 class="text-muted"><i>Bootstrap v3.3.7 compatible</i></h3>
                </div>

                <div class="col-lg-4 col-lg-offset-4 datepicker-wrap">

                    <vue-datepicker
                        :min="'2016-12-10 10:10'"
                    ></vue-datepicker>

                </div>

            </div>


        </div>

    </div>
</template>

<script>
    import vueDatepicker from './components/vue-datepicker'

    export default {
        name: 'app',

        components: {
            vueDatepicker
        }

    }
</script>

<style>
    #app {
        font-family: 'Avenir', Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        color: #2c3e50;
        margin-top: 60px;
    }

    .logo {
        width: 200px;
    }

    .datepicker-wrap {
        border-top: 1px dashed #b4b4b4;
        margin-top: 20px;
        padding: 40px 20px;
    }
</style>
