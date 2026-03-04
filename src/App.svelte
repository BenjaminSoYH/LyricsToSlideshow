<script lang="ts">
  import Slide from './Slide.svelte';
  import SlideCard from './SlideCard.svelte';
  import { mount } from 'svelte';
  import { type songSections } from './types';
    import Row from './components/Row.svelte';
    import Column from './components/Column.svelte';


  let categeorizeLyrics = $state(false);


  let LyricsWithSections: { section: string; text: string }[] = [];
  let Lyrics: string[] = [];
  let pos = 0;

  const slideProps = $state({ lyrics: Lyrics[0] });
  let slideMounted = $state(false);
  let newTab: Window | null = null;
  let renderKey = $state(1);

  function splitTextToArrays(text: string, checkForParas: boolean = false): string[] {
    return text.split('\n\n');  
  }

  function splitTextToTypedArray(text: string): { section: string; text: string }[] {
    const result: { section: string; text: string }[] = [];
  
    const parts = text.split('\n\n');
    for (const part of parts) {

      var separator = part.indexOf('\n');
      result.push({ section: part.slice(0,separator), text: part.slice(separator+1)});
    }
    return result;
  }

  function resetValues() {
    LyricsWithSections = [];
    Lyrics = [];
    pos = 0;
    slideProps.lyrics = "";
  }

  function onCreateSlides() {

      resetValues();

      var inputValue = (<HTMLInputElement>document.getElementById("inputBox")).value;
      Lyrics = splitTextToArrays(inputValue);
      if (categeorizeLyrics) {
        LyricsWithSections = splitTextToTypedArray(inputValue);
        Lyrics = LyricsWithSections.map(item => item.text);
      }
      // console.log(LyricsWithSections);
      pos = 0;
      slideProps.lyrics = Lyrics[pos];
      renderKey *= -1;

    if (newTab && !newTab.closed) {
      return;
    }
    newTab = window.open("", "ppt");
    if (!newTab) return;
    const popup = newTab;
    newTab.document.title = "Slideshow";

    document.querySelectorAll("style").forEach((styleEl) => {
      const clone = newTab!.document.createElement("style");
      clone.textContent = styleEl.textContent;
      popup.document.head.appendChild(clone);

  document.querySelectorAll('link[rel="stylesheet"]').forEach((linkEl) => {
    const clone = popup.document.createElement("link");
    clone.rel = "stylesheet";
    clone.href = (linkEl as HTMLLinkElement).href;
    popup.document.head.appendChild(clone);
  });

    });

    mount(Slide, {
      target: newTab.document.body,
      props: slideProps
    });

    slideMounted = true;
  }



  function onGoNext() {
    if (pos < Lyrics.length - 1) {
      pos += 1;
      slideProps.lyrics = Lyrics[pos];
    }
  }
    function onGoBack() {
    if (pos > 0) {
      pos -= 1;
      slideProps.lyrics = Lyrics[pos]; 
    }
  }

  function onGoPage(i: number) {
    pos = i;
    slideProps.lyrics = Lyrics[pos];
  }

  
</script>

<main>
<Column style="align-items: center;">
  <textarea id="inputBox"></textarea>
  <Row style="justify-content: center; gap: 0.5rem;">
    <button onclick={() => (categeorizeLyrics = !categeorizeLyrics)}>{categeorizeLyrics ? 'Raw input  ' : 'Categorize'}</button>
    <button onclick={onCreateSlides}>Create Slides</button>
  </Row>
</Column>
  {#if slideMounted}
  <Row style="justify-content: center; gap: 0.5rem;">
    <button onclick={onGoBack}>Back</button>
    <button onclick={onGoNext}>Next</button>
  </Row>
    {#key renderKey}
    <SlideCard onGoPage={onGoPage} Lyrics={Lyrics} LyricsWithSections={LyricsWithSections} />
      {/key}
  {/if}


</main>

<style>
main {
  min-height: 100vh;
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  gap: 0.75rem;
}


#inputBox {
  width: min(900px, 90vw);
  height: 420px;
}

button {
  border: 1px solid #d0d7de;
  background: linear-gradient(180deg, #ffffff 0%, #f6f8fa 100%);
  color: #1f2328;
  border-radius: 999px;
  padding: 0.6rem 1rem;
  font-size: 0.95rem;
  font-weight: 600;
  letter-spacing: 0.01em;
  cursor: pointer;
  box-shadow: 0 2px 8px rgba(31, 35, 40, 0.08);
  transition: transform 120ms ease, box-shadow 120ms ease, border-color 120ms ease;
}

button:hover {
  border-color: #8c959f;
  box-shadow: 0 6px 18px rgba(31, 35, 40, 0.16);
  transform: translateY(-1px);
}

button:active {
  transform: translateY(0);
  box-shadow: 0 2px 8px rgba(31, 35, 40, 0.1);
}

button:focus-visible {
  outline: 3px solid #0969da;
  outline-offset: 2px;
}
</style>