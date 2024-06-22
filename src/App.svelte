<script lang="ts">
  import { onMount } from 'svelte';

  // Basic parser to change text colors
  let code = `let a = 5;
for(val in a){
  val += 21;
}
let k = 10;
`;
  console.log("initial code");
  console.log(code);

  function parseCode(code: string) {
    const tokens = code.split(/(\s+|\b|\n)/);
    // console.log(tokens);
    return tokens.map(token => {
      if (token.startsWith('\n')) {
        return '<br>' + '&nbsp;'.repeat(token.length - 1);
      } else if (token === '\t') {
        return '&nbsp;&nbsp;&nbsp;&nbsp;'; // Representing a tab character as 4 spaces in HTML
      } else if (token.trim() === 'let') {
        return `<span class="keyword">${token}</span>`;
      } else if (!isNaN(parseFloat(token))) {
        return `<span class="literal">${token}</span>`;
      } else if (/\b[a-zA-Z_][a-zA-Z0-9_]*\b/.test(token.trim())) {
        return `<span class="ident">${token}</span>`;
      } else {
        return token; // Return the token unchanged if it doesn't match any rules
      }
    }).join('');
  }

  let highlightedCode = parseCode(code);

  // Function to update code and re-parse for highlighting
  function updateCode(newCode: string) {
    code = newCode;
    console.log(code)
    highlightedCode = parseCode(code);
  }

  onMount(() => {
    const editableArea = document.getElementById('editable-code');
    if (editableArea) {
      editableArea.innerHTML = highlightedCode;
      editableArea.contentEditable = 'true';
      editableArea.addEventListener('input', () => {
        if (editableArea.innerText !== null) {
          updateCode(editableArea.innerText);
          let selection = window.getSelection();
          let range:Range | null = null;
          if(selection){
            range = selection.rangeCount > 0 ? selection.getRangeAt(0) : null;
          }
          editableArea.innerHTML = highlightedCode;
          if(selection && range){
            console.log(range);
            selection.removeAllRanges();
            selection.addRange(range);
            console.log("added range to selection");
          }
        }
      });
    }
  });
</script>

<main>
  <div
    class="bg-gray-600 text-neutral-50 p-5 m-10 mx-auto w-2/3 h-2/3 rounded-xl shadow-black font-sans"
  >
    <div id="editable-code" class="bg-inherit focus:outline-none hover:none"></div>
  </div>
</main>

<!-- way that a grid is done-->
<style lang="postcss">
  :global(html) {
    background-color: theme(colors.zinc.200);
    background-image: radial-gradient(
      theme(colors.zinc.500) 0.5px,
      transparent 0
    );
    background-size: 30px 30px;
    background-position: -19px -19px;
  }

  :global(.keyword) {
    color: theme(colors.red.400);
  }
  :global(.ident) {
    color: theme(colors.yellow.400);
  }
  :global(.literal) {
    color: theme(colors.orange.400);
  }
  
  .keyword {
    color: theme(colors.red.400);
  }
  .ident {
    color: theme(colors.yellow.400);
  }
  .literal {
    color: theme(colors.orange.400);
  }
</style>
