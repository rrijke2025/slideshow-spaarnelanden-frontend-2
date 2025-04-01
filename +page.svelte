
<script>
  import { onMount } from 'svelte';
  let slides = [], loop = true, index = 0;

  const params = new URLSearchParams(window.location.search);
  const id = params.get('id') || 'slideshow-spaarnelanden';
  const API = 'https://slideshow-api.onrender.com/slides/' + id;

  function showNext() {
    const slide = slides[index];
    if (!slide) return;

    const container = document.getElementById('container');
    container.innerHTML = '';

    if (slide.type === 'image') {
      const img = document.createElement('img');
      img.src = slide.content;
      img.style.width = '100vw';
      img.style.height = '100vh';
      img.style.objectFit = 'cover';
      container.appendChild(img);
    } else {
      const iframe = document.createElement('iframe');
      iframe.src = slide.content;
      iframe.style.width = '100vw';
      iframe.style.height = '100vh';
      iframe.allow = 'fullscreen';
      container.appendChild(iframe);
    }

    setTimeout(() => {
      index = (index + 1) % slides.length;
      if (loop || index !== 0) showNext();
    }, (slide.duration || 10) * 1000);
  }

  onMount(async () => {
    const res = await fetch(API);
    const data = await res.json();
    slides = data.slides;
    loop = data.loop;
    index = 0;
    document.body.requestFullscreen?.();
    showNext();
  });
</script>

<div id="container" style="margin:0;padding:0"></div>

<style>
  html, body {
    margin: 0;
    overflow: hidden;
    background: black;
  }
  iframe, img {
    border: none;
  }
</style>
