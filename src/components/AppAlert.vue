<template>
<div>
    <article class="message  white-text " :class=" alert.type=='error' ? 'red': 'teal' ">
        <div class="message-header">
            <p>{{alert.header}}</p>
            <button class="delete" aria-label="delete" @click="$emit('close_alert')"><i class="  material-icons">close</i></button>
        </div>
        <div class="message-body">
            {{alert.text}} </div>
    </article>
</div>
</template>
<script>
export default {
    props: ["alert"],
    emits: ["close_alert"]
}
</script>

<style scoped>
article.message {
    position: absolute;
    width: 300px;
    top: 5%;
    z-index: 999;
    padding: 20px;
    right: 0;
}

article.message button.delete {
    position: absolute;
    top: 10px;
    right: 10px;
    background-color: transparent;
    border: none;
}
</style>
