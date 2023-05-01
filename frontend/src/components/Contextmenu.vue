<template>
   <ul class="contextmenu">
        <li @click="logInfo">Log Info</li>
        <li @click="deletePlot">Delete</li>
   </ul>
</template>
<script>
import API from '../api/API.vue';

export default {
    props : ['strapiId', 'mapboxId', 'drawRef', 'mapboxPopupRef'],
    methods: {
        deletePlot(event) {
            API.deletePlot(this.$props.strapiId)
                .then( res => {
                    this.$props.drawRef.delete(this.$props.mapboxId);
                    this.$props.mapboxPopupRef.remove();
                })
        },
        logInfo() {
            console.log(`Strapi ID: ${this.$props.strapiId}`);
            console.log(`Mapbox ID: ${this.$props.mapboxId}`);
            this.$props.mapboxPopupRef.remove();
        } 
    }
}
</script>

<style>
.contextmenu {
    list-style-type: none;
    padding-inline-start: 10px;
    margin: 5px 10px 0 0;
}

ul.contextmenu li {
    font-size: 10pt;
}

ul.contextmenu li:hover {
    background-color: lightgray;
    cursor: pointer;
}

</style>