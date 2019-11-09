<template>
    <section>
        <b-tabs>
            <b-tab-item label="Home" >
                <slot name="homeTab"></slot>
            </b-tab-item>
            <b-tab-item label="Tools">
                Tools
                <slot></slot>
            </b-tab-item>
        </b-tabs>
    </section>
</template>

<script>
export default {
   data: function () {
        return {
        }
    },
}
</script>

<style>
.tabs ul{
    background: var(--tabs-background);
    border-bottom-color: var(--tabs-background);
}
.tabs a{
    color: var(--tabs-color);
    border-bottom-color: var(--tabs-background);
}
.tabs li.is-active a{
    color: var(--tabs-active);
    border-bottom-color: var(--tabs-active);
}
.b-tabs .tab-content{
    background: var(--tabs-background);
    color: var(--tabs-active);
}
</style>