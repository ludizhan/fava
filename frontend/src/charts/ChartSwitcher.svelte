<script lang="ts">
  import { _ } from "../i18n";
  import type { KeySpec } from "../keyboard-shortcuts";
  import { keyboardShortcut } from "../keyboard-shortcuts";
  import { lastActiveChartName, showCharts } from "../stores/chart";

  import Chart from "./Chart.svelte";
  import ConversionAndInterval from "./ConversionAndInterval.svelte";

  import type { FavaChart } from ".";

  export let charts: readonly FavaChart[];

  $: active_chart =
    charts.find((c) => c.name === $lastActiveChartName) ?? charts?.[0];

  // Get the shortcut key for jumping to the previous or next chart.
  $: shortcut = (index: number): KeySpec | undefined => {
    const current = charts.findIndex((e) => e === active_chart);
    if (index === (current - 1 + charts.length) % charts.length) {
      return { key: "C", note: _("Previous") };
    }
    if (index === (current + 1 + charts.length) % charts.length) {
      return { key: "c", note: _("Next") };
    }
    return undefined;
  };
</script>

{#if active_chart}
  <Chart chart={active_chart}>
    <ConversionAndInterval />
  </Chart>
  <div hidden={!$showCharts}>
    {#each charts as chart, index}
      <button
        type="button"
        class="unset"
        class:selected={chart === active_chart}
        on:click={() => {
          $lastActiveChartName = chart.name;
        }}
        use:keyboardShortcut={shortcut(index)}
      >
        {chart.name}
      </button>
    {/each}
  </div>
{/if}

<style>
  div {
    margin-bottom: 1.5em;
    font-size: 1em;
    color: var(--text-color-lightest);
    text-align: center;
  }

  button {
    padding: 0 0.5em;
  }

  button + button {
    border-left: 1px solid var(--text-color-lighter);
  }

  button.selected,
  button:hover {
    color: var(--text-color-lighter);
  }
</style>
