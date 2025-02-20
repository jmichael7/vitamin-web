<script>
  import VtmnButton from '../../actions/VtmnButton/VtmnButton.svelte';
  import { createEventDispatcher, onDestroy, onMount } from 'svelte';
  import { cn } from '../../../utils/classnames';
  import { VTMN_ALERT_VARIANT } from './enums';

  /**
   * @type {'info'|'success'|'danger'|'warning'} variant of the alert
   * @defaultValue info
   */
  export let variant = VTMN_ALERT_VARIANT.INFO;

  /**
   * @type {string} title of the alert
   * Can also be set with a slot="title"
   */
  export let title = undefined;

  /**
   * @type {string} description of the alert
   * Can also be set with a slot="description"
   */
  export let description = undefined;

  /**
   * @type {boolean} display with a close button
   */
  export let withCloseButton = false;

  /**
   * @type {number} time (ms) before the alert disappears
   * Can't be above 8000ms.
   * Set to 0 to keep the alert visible
   */
  export let timeout = 8000;

  /**
   * @type {string} aria label on the button
   */
  export let ariaLabelCloseButton = 'Close alert';

  let className = undefined;
  /**
   * @type {string} Custom classes to apply to the component.
   */
  export { className as class };
  let timeoutId;
  const dispatch = createEventDispatcher();
  const closeHandler = () => dispatch('close');
  const _clearTimeout = () => timeoutId && clearTimeout(timeoutId);
  const _setTimeout = () =>
    (timeoutId = timeout && setTimeout(closeHandler, timeout));
  onDestroy(_clearTimeout);
  onMount(_setTimeout);
  $: componentClass = cn(
    'vtmn-alert',
    timeout > 0 && 'show',
    variant && `vtmn-alert_variant--${variant}`,
    className,
  );
</script>

<div class={componentClass} role="alert" tabindex="-1" {...$$restProps}>
  <div class="vtmn-alert_content" role="document">
    <div class="vtmn-alert_content-title">
      {#if $$slots.title}
        <slot name="title" />
      {:else}
        {title}
      {/if}
      {#if withCloseButton}
        <VtmnButton
          aria-label={ariaLabelCloseButton}
          variant="ghost-reversed"
          size="small"
          iconAlone="close-line"
          on:click={closeHandler}
        />
      {/if}
    </div>
    {#if description || $$slots.description}
      <span class="vtmn-alert_content-description">
        {#if $$slots.description}
          <slot name="description" />
        {:else}
          {description}
        {/if}
      </span>
    {/if}
  </div>
</div>

<style>
  @import '@vtmn/css-alert';
</style>
