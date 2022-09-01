<script lang="ts">
  import { onMount } from "svelte";
  import sanityClient from "@sanity/client";
  import type { Job } from "./types/Job";
  import JobRow from "./components/JobRow.svelte";
  import { GradientHeading, ProgressBar, Avatar } from '@brainandbones/skeleton';

  const sanity = sanityClient({
		apiVersion: 'v2021-03-25',
		projectId: "6d1129pd",
		dataset: "production",
		useCdn: false,
	});

	let jobs: Job[] = [];

	async function fetchJobs(): Promise<any> {
		const query = '*[_type == "job"] | order(company asc) { company, role, platform, interviewed, num_interviews, status, notes }';
		jobs = await sanity.fetch(query);
    return jobs;
	}

  function getStats(response) {
    let total_interviews = 0;
    let total_ghosts = 0;
    let total_rejects = 0;

    [...response].forEach(job => {
      if (job.interviewed && job.num_interviews) {
        total_interviews += job.num_interviews;
      }

      if (job.status === 'ghosted'){
        total_ghosts += 1;
      }

      if (job.status === 'rejected_interview' || job.status === 'rejected') {
        total_rejects += 1;
      }
    })

    return [total_interviews, total_ghosts, total_rejects]
  }

  let tablePromise: Promise<any> = fetchJobs();

</script>

<main>
  <div>
   <h1>Job Summer(y)</h1>
  </div>

  <div class="emoji-key" aria-hidden="true">
    <h4>LEGEND</h4>
    <ul>
      <li><span>❌</span> Rejected without interview</li>
      <li><span>👎️</span> Rejected after interview(s)</li>
      <li><span>👻</span> Ghosted</li>
      <li><span>🙅‍♀️</span> Not a good fit</li>
      <li><span>🥳</span> Offer received</li>
    </ul>
  </div>

  <div class="stats-wrapper">


    <!-- svelte-ignore empty-block -->
    {#await tablePromise}
      {:then response}
      <div class="stats-wrapper">
        <div class="mt-8 flex justify-center gap-x-8">
          <div class="flex flex-col items-center justify-center gap-y-2 w-48">
              <GradientHeading tag="h2" direction="bg-gradient-to-r" from="from-primary-500" to="to-accent-500">Applications Submitted</GradientHeading>
              <Avatar initials="{jobs.length.toString()}" size="2xl" background="bg-primary-500" outlined={false} hover={false} />
          </div>
          <div class="flex flex-col items-center justify-center gap-y-2 w-48">
            <GradientHeading tag="h2" direction="bg-gradient-to-r" from="from-primary-500" to="to-accent-500">
              Total # of Interviews:
            </GradientHeading>
            <Avatar initials="{getStats(response)[0].toString()}" size="2xl" background="bg-accent-500" outlined={false} hover={false} />
          </div>
          </div>
        
        <div class="mt-8 flex justify-center gap-x-8">
          <div>
            <ProgressBar label="Ghost Rate ({(getStats(response)[1] * 100) / jobs.length}%)" value={(getStats(response)[1] * 100) / jobs.length} max={100} height="h-2" color="bg-accent-500" />
          </div>
          <div>
          <ProgressBar label="Rejection Rate ({(getStats(response)[2] * 100) / jobs.length}%)" value={(getStats(response)[2] * 100) / jobs.length} max={100} height="h-2" color="bg-accent-500" />
            
            </div>
          </div>
      </div>
      {:catch error}
      <p>Nothing</p>
    {/await}
  </div>

  <div class="table-wrapper max-w-">
  {#await tablePromise}
   <p class="text-center">Loading...</p>
    {:then response}
    <div class="job-table bg-white shadow-md rounded my-6">
      <table class=" min-w-max w-full table-auto">
        <thead>
          <tr class="bg-gray-200 text-gray-600 uppercase text-sm leading-normal">
              <th class="py-3 px-6 text-left">Company</th>
              <th class="py-3 px-6 text-left">Platform</th>
              <th class="py-3 px-6 text-center">Interviewed?</th>
              <th class="py-3 px-6 text-center">Status</th>
            </tr>
        </thead>
        <tbody class="text-gray-600 text-sm font-light">
        {#each jobs as job}
        <JobRow 
          company={job.company}
          role={job.role}
          platform={job.platform}
          interviewed={job.interviewed}
          num_interviews={job.num_interviews}
          status={job.status}></JobRow>
        {/each}
        </tbody>
      </table>
    </div>
    {:catch error}
      <p style="text-center text-warning-500">{error.message}</p>
    {/await}
    </div>
</main>

<style>
  .table-wrapper {
    @apply max-w-screen-md mx-auto flex flex-col flex-wrap;
  }
  .job-table {
    @apply mx-auto max-w-xs md:max-w-none overflow-x-scroll md:overflow-auto;
  }
  .emoji-key {
    @apply max-w-screen-md mx-auto border border-gray-200 rounded-md p-4 my-4;
  }
  .emoji-key h4 {
    @apply text-sm 
    uppercase text-gray-100 p-1 px-2 
    rounded-md bg-gray-400 inline-block;
  }
  .emoji-key ul {
    @apply inline-flex flex-wrap md:flex-nowrap text-xs m-0;
  }
  .emoji-key li {
    @apply p-2;
  }
</style>