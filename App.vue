<template>
	<div>
		<Histogram :data="{apples: 10, oranges: 5, pears: 15}" />
	</div>
</template>

<script>
import Histogram from "./Histogram";

export default {
	components: {
		Histogram
	}
}
</script>