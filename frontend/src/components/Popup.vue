<template>
    <div id="popup">
            <div class="inner" :class="{ visible: isVisible }">
                <div>
                    <h3 class="header green">{{status}}</h3>
                    <p >{{message}}</p>
                </div>
            </div>
    </div>
</template>
<script>
export default {
    props : ['message', 'status', 'isVisible'],
}
</script>

<style>
#popup .inner.visible {
    opacity: 1;
}
#popup {
    display: flex;
    justify-content: center;
    overflow: visible;
}
#popup .inner{
    border-radius: 3px;
    box-shadow: 0px 0px 5px lightgrey;
    background-color: white;
    width: 300px;
    transition: opacity 1s ease-in-out;
    opacity: 0;
    position: absolute;
    top: 10%;
    left: 39%;
    z-index: 1;
}
#popup .inner div{
    text-align: left;
    padding: 12pt
}
#popup .inner .header{
    display: flex;
    justify-content: center;
    font-weight: bold;
}
</style>