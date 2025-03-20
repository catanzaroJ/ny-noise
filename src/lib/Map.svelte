<script>
  import { onMount, onDestroy } from "svelte";
  import { geoMercator, geoPath, scaleSequential, interpolateRgb } from "d3";

  let width = 600;
  let height = 700;
  let geojson = null;
  let neighborhoods = [];
  let projection, pathGenerator;
  let hoveredNeighborhood = null;
  let tooltipX = 0, tooltipY = 0;
  let container;
  let isMounted = false;
  let decibelRange = [30, 120];

  function updateSize() {
    if (container) {
      width = container.clientWidth;
      height = container.clientHeight;
    }
  }

  const colorScale = scaleSequential(interpolateRgb("red", "blue")).domain(decibelRange);

  function getRandomDecibel() {
    return Math.floor(Math.random() * (decibelRange[1] - decibelRange[0] + 1)) + decibelRange[0];
  }

  function handleMouseMove(event, name) {
    hoveredNeighborhood = name;
    tooltipX = event.clientX + 10;  // Offset so it doesn't overlap the cursor
    tooltipY = event.clientY + 10;
  }

  function handleMouseLeave() {
    hoveredNeighborhood = null;
  }

  onMount(async () => {
    isMounted = true;

    try {
      const res = await fetch("/neighbors.geojson");
      geojson = await res.json();

      if (geojson && width && height) {
        projection = geoMercator().fitSize([width, height], geojson);
        pathGenerator = geoPath(projection);

        neighborhoods = geojson.features.map(feature => ({
          ...feature,
          path: pathGenerator(feature),
          name: feature.properties.neighborhood,
          decibel_level: getRandomDecibel(),
          color: colorScale(getRandomDecibel())
        }));
      }

      updateSize();
      window.addEventListener("resize", updateSize);
    } catch (err) {
      console.error("Failed to load GeoJSON:", err);
    }
  });

  onDestroy(() => {
    if (isMounted) {
      window.removeEventListener("resize", updateSize);
    }
  });
</script>

{#if isMounted}
  <main bind:this={container}>
    <svg {width} {height}>
      {#each neighborhoods as { name, path, color, decibel_level }}
        <path
          d={path}
          fill={color} 
          stroke="black"
          opacity="0.7"
          on:mouseenter={(event) => handleMouseMove(event, name)}
          on:mousemove={(event) => handleMouseMove(event, name)}
          on:mouseleave={handleMouseLeave}
        />
      {/each}
    </svg>

    <!-- Tooltip (now positioned absolutely) -->
    {#if hoveredNeighborhood}
      <div class="tooltip" style="top: {tooltipY}px; left: {tooltipX}px;">
        {hoveredNeighborhood}
      </div>
    {/if}

    <!-- Legend -->
    <div class="legend-container">
      <div class="legend-title">Noise Levels (dB)</div>
      <div class="legend-gradient"></div>
      <div class="legend-labels">
        <span>{decibelRange[0]} dB</span>
        <span>{decibelRange[1]} dB</span>
      </div>
    </div>
  </main>
{/if}

<style>
  main {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100vh;
    overflow: hidden;
  }

  svg {
    display: block;
    width: 600px;
    height: 700px;
  }

  path {
    cursor: pointer;
    transition: opacity 0.3s ease-in-out;
  }

  path:hover {
    opacity: 1.0;
    stroke-width: 2px;
    stroke: steelblue;
  }

  .tooltip {
    position: absolute;
    background: rgba(0, 0, 0, 0.8);
    color: white;
    padding: 5px 10px;
    border-radius: 5px;
    font-size: 14px;
    pointer-events: none;
    white-space: nowrap;
  }

  .legend-container {
    margin-top: 15px;
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  .legend-title {
    font-size: 14px;
    font-weight: bold;
    margin-bottom: 5px;
  }

  .legend-gradient {
    width: 200px;
    height: 20px;
    background: linear-gradient(to right, red, blue);
    border-radius: 5px;
  }

  .legend-labels {
    display: flex;
    justify-content: space-between;
    width: 200px;
    font-size: 12px;
    margin-top: 5px;
  }
</style>
