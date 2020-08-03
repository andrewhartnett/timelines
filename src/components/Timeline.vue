<template>

    <div class="event" :style="getStyle(start, end)">
        {{title}}
    </div>

</template>

<script>

export default {
    props: ['start', 'end', 'title', 'color'],
    methods: {
        getStyle(startYear, endYear){
            let widthPercentage = (endYear - startYear) * this.$parent.eachYearPercent;
            let marginLeft = (startYear-this.$parent.startYear) * this.$parent.eachYearPercent;

            return `width: ${widthPercentage}%; margin-left: ${marginLeft}%; background: ${this.color}`;
        }
    },
}

</script>

<style lang="scss">
.event {
  padding: 3px;
  border-radius: 5px;
  text-align: center;
}
</style>