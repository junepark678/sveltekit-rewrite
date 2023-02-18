<script lang="ts">
	import '../app.css';
	import { v4 as uuidv4 } from 'uuid';
	import { serialize as serializeXML } from '@plist/xml.serialize';
	// import Radio from './Radio.svelte'
	
	let config: string;
	let ip: string = "";
	let ip2: string = "";
	let blob: Blob;
	let url: string;
	let DoH: string;
	let generate = async () => {
		if(ip === ""){
			return;
		}
	
		var base_profile = await fetch("/base_profile.json");
		var new_profile = await base_profile.json();
		// var new_profile = base_profile.json();
	
		// console.log(serializeXML(base_profile))
	
		new_profile.PayloadDisplayName = `Custom DNS: ${ip}`;
		new_profile.PayloadContent[0].DNSSettings.DNSProtocol = type_dns;
		new_profile.PayloadIdentifier = `${uuidv4()}`;
		new_profile.PayloadUUID = `${uuidv4()}`;
		new_profile.PayloadContent[0].DNSSettings.ServerAddresses[0] = ip;
		new_profile.PayloadContent[0].DNSSettings.ServerAddresses[1] = ip2;
		if(type_dns === "HTTPS"){
			new_profile.PayloadContent[0].DNSSettings.ServerURL = DoH;
		}
		new_profile.PayloadContent[0].PayloadIdentifier = `${uuidv4()}`;
		new_profile.PayloadContent[0].PayloadUUID = `${uuidv4()}`;
		new_profile.PayloadContent[0].PayloadDisplayName = `Custom DNS: ${ip}`;
	
	
	
		blob = new Blob([serializeXML(new_profile)], {type: "application/x-apple-aspen-config"})
		
		url = URL.createObjectURL(blob);
	}
	
	let type_dns: string;
	
	</script>
	
	<main>
		<input type="radio" name="type" id="DoH" bind:group={type_dns} value="HTTPS">
		<p style="display:inline;">DoH</p>
		<input type="radio" name="type" id="DoT" style="display:inline;" bind:group={type_dns} value="TLS">
		<p style="display:inline;">DoT</p>
	
		<p>DNS: </p>
		<input bind:value={ip} on:change={generate} on:keydown={generate}>
		<!-- <button on:click={generate}>Generate!</button> -->
		<p>Fallback DNS: </p>
		<input bind:value={ip2} on:change={generate} on:keydown={generate}>
		{#if type_dns === "HTTPS"}
		<div>
			<p>DoH Server Name: </p>
			<input bind:value={DoH} on:change={generate} on:keydown={generate}>
		</div>
		{/if}
		<a href={url} style="display:block">Download!</a>
	</main>