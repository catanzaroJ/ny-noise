<script>
  let time = 0; // Initial time position, in hours (0-24)
  
  // Function to format time in 24-hour format
  const formatTime = (hour) => {
    const h = Math.floor(hour);
    const ampm = h >= 12 ? 'PM' : 'AM';
    const formattedHour = h % 12 || 12; // 12-hour format
    return `${formattedHour}:00 ${ampm}`;
  };
</script>

<div class="slider-container">
  <input
    type="range"
    min="0"
    max="24"
    step="0.1"
    bind:value={time}
    class="slider"
  />
  
  <div class="time-label">
    {formatTime(time)}
  </div>
  
  <div class="tick-marks">
    {#each Array(25) as _, index}
      {#if index % 4 === 0}
        <div class="tick" style="left: {index * 4}%"></div>
      {/if}
    {/each}
  </div>
</div>

<style>
  .slider-container {
    position: relative;
    width: 100%;
    max-width: 600px;
    margin: auto;
  }

  .slider {
    width: 100%;
    appearance: none;
    height: 10px;
    background: #ddd;
    border-radius: 5px;
    outline: none;
    transition: background 0.3s ease;
  }

  .slider:focus {
    background: #ccc;
  }

  .time-label {
    text-align: center;
    margin-top: 10px;
    font-size: 1.2em;
  }

  .tick-marks {
    position: absolute;
    top: -10px;
    left: 0;
    width: 100%;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  .tick {
    width: 2px;
    height: 10px;
    background-color: rgb(109, 89, 89);
  }
</style>
