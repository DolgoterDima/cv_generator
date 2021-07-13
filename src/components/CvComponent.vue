<template>
<div class="content cv_component left-align ">
    <h1 class="title" v-if="component.type == 'header'">{{component.text}}</h1>
    <h2 class="subtitle" v-else-if="component.type == 'subheader'">{{component.text}}</h2>
    <img class="title" v-else-if="component.type == 'image'" :src="component.text">
    <p class="title is-6" v-else-if="component.type == 'text'">{{component.text}}</p>
    <button class="btn-floating btn-small waves-effect waves-light red delete_cv_component" @click="emit_id(component.id)"> <i class="large material-icons">close</i></button>
</div>
</template>

<script>
export default {
    emits: ["delete_cv_part"],
    props: ["component"],
    data() {
        return {
            component_type: this.component.type,
        }
    },
    methods: {
        emit_id(val) {
            this.$emit("delete_cv_part", val)

        }
    },

}
</script>

<style scoped>
.cv_component:hover .delete_cv_component {
    display: block;
}

.cv_component {
    position: relative;
}

.delete_cv_component {
    position: absolute;
    right: 0;
    top: 0;
    font-size: 20px;
    display: none;
}
</style>
