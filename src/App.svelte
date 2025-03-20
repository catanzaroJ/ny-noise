<script>
  import { onMount } from 'svelte';
  import Map from "./lib/Map.svelte";
  import List from "./lib/List.svelte";
  import TimeSlider from "./lib/TimeSlider.svelte";

  let showText = false;
  let video;
  let overlayVisible = true;
  let scrollY = 0;
  let showOverlayText = false;
  let sections = [
    { id: 1, title: 'Introduction', content: 'Noise pollution is a growing concern in urban environments, affecting both quality of life and public health. This interactive map visualizes noise levels across different neighborhoods in New York City, helping us understand where noise pollution is most concentrated.', showGraph: false },
    { 
      id: 2, 
      title: 'New York City Noise Pollution', 
      content: `Each neighborhood is shaded according to its estimated noise level, measured in decibels (dB). Higher decibel levels indicate louder environments, which can be influenced by factors like traffic, construction, and nightlife. By exploring this map, you can identify areas with excessive noise and compare different regions of the city`,
      showGraph: false 
    },
    { id: 3, title: 'Conclusion', content: 'Use your mouse to hover over a neighborhood and reveal its name and estimated noise level. This visualization provides insight into the varying soundscapes of New York City, highlighting areas where noise pollution is a significant issue.', showGraph: false }
  ];

  // Sliding animation state
  let slideIn = false;
  let showFact = false;

  const handleScroll = () => {
    scrollY = window.scrollY;
    const opacity = Math.max(1 - scrollY / 500, 0);
    if (video) {
      video.style.opacity = opacity; // Adjust the opacity of the video on scroll
    }
  };

  onMount(() => {
    setTimeout(() => {
      overlayVisible = false;
    }, 1200);

    if (video) {
      video.muted = true;
      video.play().then(() => {
        video.muted = false;
      }).catch(err => {
        console.error("Autoplay failed:", err);
      });
    }

    setTimeout(() => {
      showOverlayText = true;
    }, 5000);

    setTimeout(() => {
      slideIn = true;
      showFact = true;
    }, 2000); // Triggering the fact to slide in after 2 seconds

    window.addEventListener("scroll", handleScroll);
    return () => {
      window.removeEventListener("scroll", handleScroll);
    };
  });
</script>

<style>
  .video-container {
    position: fixed;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
    overflow: hidden;
    z-index: -1; /* Ensure the video is behind the content */
  }

  video {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 100vw;
    height: 100vh;
    object-fit: cover; /* Ensures the video covers the entire screen */
  }

  .overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
    background: black;
    z-index: 10;
    opacity: 1;
    transition: opacity 1.2s ease-out;
  }

  .overlay.hidden {
    opacity: 0;
    pointer-events: none;
  }

  .overlay-text {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    color: white;
    font-size: 2rem;
    background: rgba(0, 0, 0, 0.6);
    padding: 20px;
    border-radius: 10px;
    opacity: 0;
    transition: opacity 1s ease-in-out;
    z-index: 2;
  }

  .show-text {
    opacity: 1;
  }

  .content {
    position: relative;
    z-index: 2;
    min-height: 250vh;
    display: flex;
    align-items: center;
    justify-content: center;
    flex-direction: column;
  }

  .container {
    height: 800px;
    display: flex;
    flex-direction: row;
    justify-content: space-between;
  }

  .pane {
    padding: 20px;
  }

  .left {
    width: 30%; /* Make the left pane smaller */
    background-color: #f4f4f4;
  }

  .right {
    width: 75%; /* Make the right pane take up the remaining space */
    background-color: #e0e0e0;
  }

  /* Sliding component styles */
  .fact-slide {
    position: absolute;
    top: 30%;
    left: 10%;
    transform: translateX(-100%);
    transition: transform 1s ease-in-out;
    z-index: 20;
    background-color: rgba(0, 0, 0, 0.8);
    color: white;
    padding: 20px;
    border-radius: 10px;
  }

  .fact-slide.show {
    transform: translateX(0);
  }

  .fact-text {
    font-size: 1.5rem;
  }

  .stat-container {
    display: flex;
    justify-content: space-around;
    margin-top: 50px;
    opacity: 0;
    transition: opacity 1s ease-in-out;
  }

  .stat-container.show {
    opacity: 1;
  }

  .stat {
    background: #f4f4f4;
    padding: 20px;
    border-radius: 5px;
    width: 30%;
  }

  .stat h3 {
    margin-bottom: 10px;
  }
</style>

<div class="overlay {overlayVisible ? '' : 'hidden'}"></div>
<div class="video-container">
  <video bind:this={video} autoplay muted loop playsinline>
    <source src="/trafficvid.mp4" type="video/mp4" />
  </video>
</div>

<div class="overlay-text {showOverlayText ? 'show-text' : ''}">
  "New York City: A city that never sleeps, but it also never stops making noise."
</div>

<div class="content">
  <div style="height: 100vh;"></div>
  <p class="stat-text {showText ? 'show' : ''}">
    "New York City, known for its vibrant energy, is also home to a significant noise pollution problem..."
  </p>

  {#each sections as { id, title, content, showGraph }}
    <div class="article-section" id={"section-" + id}>
      <h2>{title}</h2>
      <p>{content}</p>
      {#if showGraph}
        <div class="graph">
          <p>Graph for {title} (or any other content)</p>
        </div>
      {/if}
    </div>
  {/each}
  
  
  
  <!-- Sliding stats/graphs -->
  <div class="stat-container {slideIn ? 'show' : ''}">
    <div class="stat">
      <h3>Average Noise Levels</h3>
      <p>In certain areas of NYC, noise levels can exceed 85 dB regularly, which is harmful to health.</p>
    </div>
    <div class="stat">
      <h3>Health Impacts</h3>
      <p>Continuous exposure to high noise levels can lead to hearing loss, stress, and heart disease.</p>
    </div>
    <div class="stat">
      <h3>Neighborhoods Affected</h3>
      <p>Midtown Manhattan, the Financial District, and parts of Brooklyn suffer from the highest noise levels.</p>
    </div>
  </div>
</div>

<div class="container">
  <div class="pane left">
    <List />
  </div>
  <div class="pane right">
    <Map />
  </div>
</div>

<!-- TimeSlider component displayed below Map -->
<TimeSlider />
