<template>
    <div class="near map-positon">
        <p>{{ currentCity }}</p>
        <p>{{ currentCity_detail }}</p>
    </div>
</template>


<script>
    import axios from 'axios';
    export default {
        data() {
            return {
                citylocation: null,
                lat: 0,
                lng: 0,
                currentCity: "--",
                currentCity_detail: ''
            }
        },
        created() {
            this.getIp();                 
        },
        computed: {
        },
        methods: {
            getIp:function(){
              alert(1)
              var url="http://chaxun.1616.net/s.php?type=ip&output=json&callback=?";
              var params="";
              axios.post(url, params)
                .then(response => {
                    console.log(response)
                }, err => {
                    console.log(err)
                })
                .catch((error) => {
                   console.log(error)
                })  
            }
        },
        mounted() {
        }
    }

</script>