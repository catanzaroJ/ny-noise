<script>
    export let items = ['Statue of Liberty', 'Central Park', 'Brooklyn Bridge', 'Empire State Building', 'Times Square', 
      'Metropolitan Museum of Art'
    ];
  </script>
  
  <style>
    .list-container {
      padding: 50px;
      background: rgba(255, 255, 255, 0.8);
      border-radius: 10px;
      max-width: 300px;
    }
  </style>
  
  <div class="list-container">
    <h3>Landmarks</h3>
    <ul>
      {#each items as item}
        <li>{item}</li>
      {/each}
    </ul>
  </div>
  
  