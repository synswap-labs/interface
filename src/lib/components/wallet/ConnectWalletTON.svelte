<script>
	import { shortAccountString } from '$lib/pkg/utils';
	import QRCodeStyling from 'qr-code-styling';
	import * as QROptions from './qr.json';
	import BifrostLogo from '$lib/images/bifrost_logo.svg';
	import TonkeeperLogo from '$lib/images/tonkeeper_logo.png';
	import { TON } from '$lib/stores.js';
	import HashIcon from '../HashIcon.svelte';

	const { connected, address, wallets } = $TON;

	export let connectedWallet;

	const qrCode = new QRCodeStyling(QROptions);

	const onConnected = () => {
		connectedWallet = wallets.TonKeeper;
		document.getElementById('qr-modal').checked = false;
	};

	let isConnectingModalOpen = false;
	let isDisconnectingModalOpen = false;

	const connect = async () => {
		let connectionLink = await wallets.TonKeeper.connectExternal(onConnected);

		qrCode.update({
			data: connectionLink,
			image: BifrostLogo
		});
		qrCode.append(document.getElementById('qr-code'));

		document.getElementById('qr-modal').checked = true;

		isConnectingModalOpen = false;
	};
	const disconnect = async () => {
		await TON.disconnect();
		isDisconnectingModalOpen = false;
	};
</script>

<div class="w-full">
	{#if !$connected}
		<label for="connect-modal-ton" class="btn btn-sm w-full btn-secondary">Connect Wallet</label>
	{:else}
		<label for="disconnect-modal-ton" class="btn btn-sm w-full btn-secondary gap-2"
			><HashIcon value={$address} size={25} />{shortAccountString(5, 5, $address ?? '')}</label
		>
	{/if}
</div>

<!--- Choose wallet modal -->

<input
	type="checkbox"
	id="connect-modal-ton"
	class="modal-toggle"
	bind:checked={isConnectingModalOpen}
/>
<div class="modal modal-bottom sm:modal-middle">
	<div class="modal-box">
		<h3 class="font-bold text-lg">Connect wallet</h3>
		<label for="connect-modal-ton" class="btn btn-sm absolute right-2 top-2">✕</label>
		<div class="flex py-4 justify-center flex-col gap-2">
			<button class="btn btn-block" on:click={connect}>
				<img class="mr-2" src={TonkeeperLogo} alt="tonkeeper" width={35} />
				Tonkeeper
			</button>
		</div>
	</div>
</div>

<!--- Disconnect modal -->

<input
	type="checkbox"
	id="disconnect-modal-ton"
	class="modal-toggle"
	bind:checked={isDisconnectingModalOpen}
/>
<div class="modal modal-bottom sm:modal-middle">
	<div class="modal-box">
		<h3 class="font-bold text-lg">You are going to disconnect</h3>
		<label for="disconnect-modal-ton" class="btn btn-sm absolute right-2 top-2">✕</label>
		<div class="modal-action">
			<button class="btn btn-sm" on:click={disconnect}>Disconnect</button>
		</div>
	</div>
</div>

<!--- QR modal -->

<input type="checkbox" id="qr-modal" class="modal-toggle" />
<label for="qr-modal" class="modal cursor-pointer">
	<label
		class="modal-box relative w-fit text-center flex flex-col gap-2 items-center py-4 px-6"
		for=""
	>
		<h3 class="text-lg font-bold">Connect TON</h3>
		<p class="text-sm w-56">Scan the QR code with your phone's camera or Tonkeeper.</p>
		<div id="qr-code" />
		<p class="text-sm w-56">We do not store your wallet credentials, so your TON is safe.</p>
	</label>
</label>
