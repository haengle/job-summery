<script type="ts">
    import Emoji from "./Emoji.svelte";
    import { Avatar, Badge } from '@brainandbones/skeleton';
    export let company : string, role : string, platform : string, interviewed : boolean, num_interviews : number = 0, status : string;

    let emojiGroup = ['❌', '👎️', '👻', '🙅‍♀️', '🥳'];
    let emojiSymbol : string, emojiLabel: string;

    switch(status) {
      case "rejected_interview":
        emojiLabel = 'rejected';
        emojiSymbol = emojiGroup[1]
        break;
      case "rejected":
        emojiLabel = 'rejected';
        emojiSymbol = emojiGroup[0]
        break;
      case "ghosted":
        emojiLabel = 'ghosted';
        emojiSymbol = emojiGroup[2]
        break;
      case "declined":
      emojiLabel = 'declined';
        emojiSymbol = emojiGroup[3]
        break;
      case "offer":
      emojiLabel = 'offer';
        emojiSymbol = emojiGroup[4]
        break;
      default: 
        ""
    }

    
  </script>
  
  <tr class="border-b border-gray-200 hover:bg-gray-100">
    <td class="py-3 px-6 text-left">
        <div class="flex flex-col">
            <h3 class="lg:text-base font-bold">
                {company}
            </h3>
            <h4>
                {role}
            </h4>
        </div>
    </td>
    <td class="py-3 px-6 text-left">
        <div class="flex items-center">
        <h4>{platform}</h4>
        </div>
    </td>
    <td class="py-3 px-6 text-center">
        <div class="flex items-center justify-center">
        {#if interviewed}
            <Badge background="bg-primary-500" class="shadow-xl z-10">{num_interviews == null ? 0 : num_interviews}</Badge>
        {:else}
        <Emoji symbol="👎️" label="No"></Emoji>
        {/if}
        </div>
    </td>
    <td class="py-3 px-6 text-center">
        <div class="flex items-center justify-center">
        <Emoji symbol={emojiSymbol} label={emojiLabel}></Emoji>
        </div>
    </td>
</tr>
