<template lang="pug">
div()

	a(:class="'vw-btn-'+module.title", v-html="module.icon", @click="onBtnClick")

	.dashboard(
		v-if="showDashboard",
		ref="dashboard"
	)
		component(
      ref="moduleDashboard",
      :is="module",
      @exec="exec",
      :uid="uid",
      :options="options",
	  @closedashboard="closeDashboard"
    )

</template>
<script>
import bus from 'src/editor/bus.js';

export default {
	props: ["module", "options"],

	data () {
		return {
			showDashboard: false,
		}
	},

  computed: {
    uid () {
      return this.$parent._uid
    }
  },

	methods: {
		closeDashboard () {
			this.showDashboard = false;
		},

		openDashboard () {
			this.showDashboard = true;
		},

		exec () {
			this.$parent.exec.apply(null, arguments)
		},

		onBtnClick ($event) {
			$event.preventDefault();
			if (this.module.action !== undefined)
				this.exec.apply(null, this.module.action);

			else if (this.module.customAction !== undefined) {
				this.module.customAction(bus.utils).forEach(a => this.exec.apply(null, a));
			}

			else if (
				(
					this.module.render !== undefined
					|| this.module.prototype._render
				) &&
				(!this.$refs.dashboard || !this.$refs.dashboard.contains($event.target))
			) {
				this.showDashboard = !this.showDashboard;
				bus.emit(`${this.uid}_${this.showDashboard ? "show" : "hide"}_dashboard_${this.module.title}`);
				return;
			}
		}
	}
}
</script>
