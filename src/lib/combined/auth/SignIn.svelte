<script>
	import { enhance, applyAction } from '$app/forms';
	import { onMount } from 'svelte';

	import OverlayScriptLoader from '../../components/overlay/OverlayScriptLoader.svelte';
	import TextInput from '../../components/inputs/TextInput.svelte';
	import GoogleIcon from '../../icons/GoogleIcon.svelte';
	import newUniqueId from 'locally-unique-id-generator'
	import log from 'loglevel';

	/*
	 * rootId is used to have different ids when we have multiple signIn buttons (some hidden at some conditions) then we may want to trigger the opening of a popin with a specific id.
	 * We then have to specify that id to the script: <Button color='light' data-hs-overlay={'#'+rootId}>. Check Overlay.js for more details.
	 */
	export let rootId='hs-modal-signIn', signInActionUrl, signUpActionUrl, resetPassUrl, privacyUrl, open = false, providersUrls;
	let overlay, rememberMe = true, termsAccepted = false, signInMode = true, pass = '', passConf = '', samePass = true,
		validPass = true,
		colors = undefined;
	let responseError, form, submit;
	$:formUrl = signInMode ? signInActionUrl : signUpActionUrl;
	const rememberMeId = 'remember-me'+ newUniqueId()

	onMount(() => {
		if (open) {
			window.HSOverlay.open(overlay);
		}
	});

	function switchSignMode(event) {
		signInMode = !signInMode;
	}

	function checkPasswordsAreTheSame(e) {
		passConf = e.target.value;
		samePass = (pass === passConf);
	}

	function checkPasswordStrengthOk() {
		validPass = passwordStrength(pass) > 2;
	}

	function checkPasswordStrength(e) {
		pass = e.target.value;
		const strength = passwordStrength(pass);
		let borderColor = 'blue-500';
		if (strength < 2) {
			borderColor = 'red-500';
		} else if (strength == 2) {
			borderColor = 'red-200';
		} else if (strength == 3) {
			borderColor = 'green-300';
		} else if (strength == 4) {
			borderColor = 'green-500';
		}
		colors = { bd: borderColor };
	}

	function passwordStrength(password) {
		let strength = 0;

		// Check for minimum length
		if (password.length >= 8) {
			strength++;
		}

		// Check for at least one uppercase and one lowercase character
		if (/[A-Z]/.test(password) && /[a-z]/.test(password)) {
			strength++;
		}

		// Check for at least one number
		if (/\d/.test(password)) {
			strength++;
		}

		// Check for at least one special character
		if (/[^A-Za-z0-9]/.test(password)) {
			strength++;
		}

		return strength;
	}

	/**
	 * Known errors
	 * Known errors for databases related actions are:
	 * Duplicate key on user and key creation (AUTH_DUPLICATE_KEY_ID)
	 * Invalid user id (AUTH_INVALID_USER_ID) ???
	 * Invalid keys (AUTH_INVALID_KEY_ID)
	 * Expired keys (AUTH_EXPIRED_KEY)
	 * Duplicate session id on session creation and renewal (AUTH_DUPLICATE_SESSION_ID)
	 *
	 * https://lucia-auth.com/basics/error-handling?sveltekit
	 *
	 * @returns {(function({result?: *}): void)|*}
	 */
	function formSubmitted() {
		return async ({ result }) => {
			form.action = formUrl; //this is to cancel the effect of an eventual call to resetPassword()
			log.debug('result returned: ', result);
			if (result.type === 'success') {
				window.location.reload();
			} else if (result.type === 'failure') {
				const error = result.data.error;
				if (error === 'AUTH_DUPLICATE_KEY_ID') {
					responseError = 'User already exists';
				} else if (error === 'AUTH_INVALID_USER_ID') {
					responseError = 'User email is invalid';
				} else
					responseError = error;
			}
			await applyAction(result);
		};
	}

	/**
	 * This is a hack: it allows us to save code by sending the form as a 'forgot password':
	 * it has the drawback that once called, the form action value remains hacked until a response is received
	 */
	function resetPassword() {
		form.action = resetPassUrl;
		submit.click();
	}
</script>

<!--This is to load the script-->
<OverlayScriptLoader />

<div bind:this={overlay} id={rootId}
		 class='hs-overlay hidden w-full h-full fixed top-0 left-0 z-[60] overflow-x-hidden overflow-y-auto'>
	<div
		class='hs-overlay-open:mt-7 hs-overlay-open:opacity-100 hs-overlay-open:duration-500 mt-0 opacity-0 ease-out transition-all sm:max-w-lg sm:w-full m-3 sm:mx-auto'>
		<div class='bg-white border border-gray-200 rounded-xl shadow-sm dark:bg-gray-800 dark:border-gray-700'>
			<div class='p-4 sm:p-7'>
				<div class='text-center'>
					<h2
						class='block text-2xl font-bold text-gray-800 dark:text-gray-200'>{signInMode ? 'Sign in' : 'Sign up'}</h2>
					<p class='mt-2 text-sm text-gray-600 dark:text-gray-400'>
						{signInMode ? 'Don\'t have an account yet?' : 'Already have an account?'}
						<span class='cursor-pointer text-blue-600 decoration-2 hover:underline font-medium'
									on:click={switchSignMode}>
							{signInMode ? 'Sign up here' : 'Sign in here'}
						</span>
					</p>
				</div>

				<div class='mt-5'>
					<a
						class='w-full py-3 px-4 inline-flex justify-center items-center gap-2 rounded-md border font-medium bg-white text-gray-700 shadow-sm align-middle hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-offset-white focus:ring-blue-600 transition-all text-sm dark:bg-gray-800 dark:hover:bg-slate-800 dark:border-gray-700 dark:text-gray-400 dark:hover:text-white dark:focus:ring-offset-gray-800'
						href={providersUrls.googleUrl}>
						<GoogleIcon />
						{signInMode ? 'Sign in with Google' : 'Sign up with Google'}
					</a>

					<!--					<a-->
					<!--						class='w-full py-3 px-4 inline-flex justify-center items-center gap-2 rounded-md border font-medium bg-white text-gray-700 shadow-sm align-middle hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-offset-white focus:ring-blue-600 transition-all text-sm dark:bg-gray-800 dark:hover:bg-slate-800 dark:border-gray-700 dark:text-gray-400 dark:hover:text-white dark:focus:ring-offset-gray-800'-->
					<!--						href={providersUrls.lnUrl}>-->
					<!--						<IconLinker prop='linkedin' style='fill:#0077b5; width:1.2rem;margin-left: 0.4rem'/>-->
					<!--						{signInMode ? 'Sign in with Linkedin' : 'Sign up with Linkedin'}-->
					<!--					</a>-->

					<div
						class='py-3 flex items-center text-xs text-gray-400 uppercase before:flex-[1_1_0%] before:border-t before:border-gray-200 before:mr-6 after:flex-[1_1_0%] after:border-t after:border-gray-200 after:ml-6 dark:text-gray-500 dark:before:border-gray-600 dark:after:border-gray-600'>
						Or
					</div>

					<!-- Form -->
					<form method='post' action={formUrl} bind:this={form} use:enhance={formSubmitted}>
						<div class='grid gap-y-4'>
							<TextInput type='email' required label='Email address' hasError={responseError != null}
												 errorText={responseError} />
							{#if signInMode}
								<TextInput type='password' required label='Password'>
									<div slot='labelComplement'
											 class='cursor-pointer text-sm text-blue-600 decoration-2 hover:underline font-medium'
											 on:click={resetPassword}>Forgot password?
									</div>
								</TextInput>
							{:else}
								<TextInput type='password' required label='Password' {colors}
													 on:input={checkPasswordStrength}
													 value={pass} hasError={!validPass} on:blur={checkPasswordStrengthOk}
													 on:focus={()=>validPass = true}
													 errorText='Password is too week: must include capital letters, numbers and special characters' />
								<TextInput type='password' name='passConf' required label='Confirm Password'
													 on:keyup={checkPasswordsAreTheSame} on:blur={checkPasswordsAreTheSame} bind:value={passConf}
													 hasError={!samePass}
													 errorText='retyped password is different' />
							{/if}
							<!-- Checkbox -->
							{#if signInMode}
								<div class='flex items-center'>
									<div class='flex'>
										<input id={rememberMeId} name='remember-me' type='checkbox' bind:checked={rememberMe}
													 class='shrink-0 mt-0.5 border-gray-200 rounded text-blue-600 pointer-events-none focus:ring-blue-500 dark:bg-gray-800 dark:border-gray-700 dark:checked:bg-blue-500 dark:checked:border-blue-500 dark:focus:ring-offset-gray-800'>
									</div>
									<div class='ml-3'>
										<label for={rememberMeId} class='text-sm dark:text-white'>Remember me</label>
									</div>
								</div>
							{:else}
								<div class='flex items-center'>
									<div class='flex'>
										<input bind:checked={termsAccepted} id='accept-conditions' name='accept-conditions' type='checkbox'
													 class='shrink-0 mt-0.5 border-gray-200 rounded text-blue-600 pointer-events-none focus:ring-blue-500 dark:bg-gray-800 dark:border-gray-700 dark:checked:bg-blue-500 dark:checked:border-blue-500 dark:focus:ring-offset-gray-800'>
									</div>
									<div class='ml-3'>
										<label for='accept-conditions' class='text-sm dark:text-white'>
											I accept the
											<a target='_blank' class='text-blue-600 underline decoration-2 hover:underline font-medium'
												 href={privacyUrl}>
												Privacy Policy
											</a>
										</label>
									</div>
								</div>
							{/if}
							<!-- End Checkbox -->
							<button type='submit' bind:this={submit}
											disabled={!signInMode && (!termsAccepted || !validPass || !samePass)}
											class='py-3 px-4 inline-flex justify-center items-center gap-2 rounded-md border border-transparent font-semibold bg-blue-500 disabled:bg-gray-100 disabled:text-gray-500 text-white hover:bg-blue-600 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2 transition-all text-sm dark:focus:ring-offset-gray-800'>
								{signInMode ? 'Sign in' : 'Sign up'}
							</button>
						</div>
					</form>
					<!-- End Form -->
				</div>
			</div>
		</div>
	</div>
</div>