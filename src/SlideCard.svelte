<script lang="ts">
interface SlideCardProps {
  onGoPage: (index: number) => void;
  Lyrics: string[];
  LyricsWithSections: { section: string; text: string }[];
}
    const { onGoPage, Lyrics = [], LyricsWithSections = [] } : SlideCardProps = $props();
    const categeorizeLyrics = (LyricsWithSections !== undefined && LyricsWithSections.length > 0)
    console.log("LyricsWithSections", LyricsWithSections);

    const colors: string[] = ["red", "blue", "green", "yellow", "orange", "purple", "pink", "brown", "black", "white"];
    let uniqueSections: string[] = [];
    if (categeorizeLyrics) {
        uniqueSections = LyricsWithSections?.map(item => item.section.split(' ')[0]) ?? [];
    }
    
    function getColor(section: string): string {
        const index = uniqueSections.indexOf(section.split(' ')[0]);
        return colors[index % colors.length];

    }
</script>

<div id="Card-Slider">
    {#if categeorizeLyrics}
        {#each LyricsWithSections as { section, text }, i}
        <button style="background-color: {getColor(section)};"
            onclick={() => onGoPage(i)}>
            {text}
        </button>
        {/each}
        {:else}
            {#each Lyrics as item, i}
            <button
                onclick={() => onGoPage(i)}>
                {item}
            </button>
        {/each}
    {/if}
</div>

<style>
    #Card-Slider {
        display: grid;
        grid-template-columns: auto auto auto;
        row-gap: 1em;
        text-align: center;
        }
</style>
