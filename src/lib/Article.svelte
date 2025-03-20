<script>
    export let title;
    export let content;
  </script>
  
  <style>
    .article {
      background-color: #fff;
      padding: 20px;
      border-radius: 8px;
      margin: 20px 0;
      box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
    }
  
    .article h2 {
      font-size: 2rem;
      color: #333;
      margin-bottom: 10px;
    }
  
    .article p {
      font-size: 1.1rem;
      color: #555;
      line-height: 1.6;
    }
  
    .article .fact {
      font-weight: bold;
      color: #e74c3c;
      margin-top: 15px;
    }
  </style>
  
  <div class="article">
    <h2>{title}</h2>
    <p>{content}</p>
    <p class="fact">Fact: Noise pollution in NYC is responsible for over 10,000 premature deaths annually.</p>
  </div>
  