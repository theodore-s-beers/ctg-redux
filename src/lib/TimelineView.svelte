<script lang="ts">
	import type { JsonStuff } from '$lib/utils.svelte';
    import {fetchList} from '$lib/utils.svelte';
    import { onMount } from 'svelte';
    import {fetchEntries} from '$lib/utils.svelte';
    let projectTitles = [];
    let dataLoaded = false;
    let projects = [];
  let start = Infinity;
  let finish = 0;
  const tags = [];
 const years = [];

   let isFunded = false;
     async function loadData() {
    const responseIndex = await fetch("https://raw.githubusercontent.com/Closing-the-Gap-in-NLS-DH/Projects/master/PROJECTS.json");
    const data = await responseIndex.json();
    Object.keys(data).map(async (uuid, key) => {
      const responseProject = await fetch(`https://raw.githubusercontent.com/Closing-the-Gap-in-NLS-DH/Projects/master${data[uuid].path}${uuid}.json`);
      const projectData = await responseProject.json();
      const period = [];
      let noEnding = false;
      projectData.project.date.map((p) => {
        if (p.from !== "") {
          let begin = Number(p.from);
          let end = -1;
          if (begin < start) start = begin;
          if (p.to !== "") {
            end = Number(p.to);
          } else noEnding = true;
          if (end > finish) finish = end;
          period.push([begin, end]);
        }
      });
      if (period.length) {
        projects.push({
          title: projectData.project.title,
          periods: period,
          noDataOnEnding: noEnding,
          source: `https://github.com/Closing-the-Gap-in-NLS-DH/Projects/blob/master${data[uuid].path}${uuid}.json`,
        }
        );
           
      }
      projectData.project.keywords.map((tag) => {
        if (!tags.includes(tag)) tags.push(tag);
       
      } );

      if (key === Object.keys(data).length - 1) {
         let j=0;
          for (let i = start; i <= finish; i += 1) {
          years[j]=i;
          j++;
        }
       projects.map((p, i) => {
           
          p.periods.map((pp, pi) => {
            if (pp[1] === -1) projects[i].periods[pi][1] = finish;
          });
          
        });
         
        projects.sort((a, b) => {
          if (a.title > b.title) return 1;
          if (a.title < b.title) return -1;
          return 0;
        });
      
    
          dataLoaded=true;
        }
       })
       }

   function isInFundingPeriod(project, year) {
    let isFunded = false;
    project.periods.forEach((period) => {
      if (year >= period[0] && year <= period[1]) {
        isFunded = true;
      }
    });
    return isFunded;
  }
   function filteredProjectYears(project, years) {
    const newYearArr = [...years];
    const yearsToDel = [];
    project.periods.forEach((period) => {
      for (let i = period[0] + 1; i <= period[1]; i += 1) {
        yearsToDel.push(i);
      }
    });
    yearsToDel.forEach((y) => {
      newYearArr.splice(newYearArr.indexOf(y), 1);
    });
    if (project.title.includes("Topoi")) 
    
    return newYearArr;
  }


  // Call the function to load data on component mount
 
   onMount(async () => {
    await loadData(); });



</script>
{#if dataLoaded}
<div  class="overflow-x-auto visualbox w-full p-5 " >
<table class=" p-5 bg-[#b8b08d] box table-fixed">
  <thead class="sticky top-0 border-black">
   <tr class="border-black">
          <th 
            colspan="{years.length + 1}"
            class="sticky top-0 py-5 px-3 text-left border-black"
          >
            <p>
              CAUTION! This timeline contains projects for which we do not have
              consistent funding information yet. Projects with unknown or
              incomplete funding data are marked with an asterisk (*).
            </p>
          </th>
    </tr>

 <tr class="table-head sticky border-y border-black bg-[#426B8C]">
    <th class="border-black">Project</th>
    {#each years as year, i}
      <th  class="sticky top-0 border-l px-3 pb-1 text-xs border-black"> {year}</th> 
    {/each}
  </tr>
  </thead>
 <tbody class="relative">
  {#each projects as p}
    <tr class="timeline-row border-b border-black">
      <td class="first-col sticky whitespace-nowrap p-2 text-left text-xs border-black"> 
      <a href="{p.source}" target="_blank">{ p.title }</a>
           {#if p.noDataOnEnding} <span>*</span>
           {/if}</td>
     {#each years as y, yKey}
    {#await isInFundingPeriod(p,y)}
    <p>Loading...</p>
  {:then result}
  {#if (isInFundingPeriod(p, y) && !p.noDataOnEnding)}
   <td class="m-1 border-l p-0 text-xs bg-[#2e4a61] border-b border-[#2e4a61] p-2"></td>
   {:else}
    <td class="m-1 border-l border-black p-0 text-xs border-b  p-2"></td>

   {/if}
  {:catch error}
    <p>Error: {error.message}</p>
  {/await}
{/each}
    </tr>
  {/each}
  </tbody>
</table>
</div>
{/if}


