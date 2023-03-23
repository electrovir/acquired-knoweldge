# Web Performance

## SVG animation performance

- applying animations to individual paths in an SVG = **BAD**, ~ 100% CPU usage for just one instance

  ```html
  <style>
    @keyframes border-jump {
      0% {
        opacity: 1;
      }

      12.49% {
        opacity: 1;
      }

      12.5% {
        opacity: 1;
      }

      40% {
        opacity: 0.3;
      }

      95% {
        opacity: 0;
      }
    }

    .loader-animated-24-icon__svg path:nth-child(0) {
      animation: border-jump 1000ms 0ms infinite linear;
    }

    .loader-animated-24-icon__svg path:nth-child(1) {
      animation: border-jump 1000ms 125ms infinite linear;
    }

    .loader-animated-24-icon__svg path:nth-child(2) {
      animation: border-jump 1000ms 250ms infinite linear;
    }

    .loader-animated-24-icon__svg path:nth-child(3) {
      animation: border-jump 1000ms 375ms infinite linear;
    }

    .loader-animated-24-icon__svg path:nth-child(4) {
      animation: border-jump 1000ms 500ms infinite linear;
    }

    .loader-animated-24-icon__svg path:nth-child(5) {
      animation: border-jump 1000ms 625ms infinite linear;
    }

    .loader-animated-24-icon__svg path:nth-child(6) {
      animation: border-jump 1000ms 750ms infinite linear;
    }

    .loader-animated-24-icon__svg path:nth-child(7) {
      animation: border-jump 1000ms 875ms infinite linear;
    }

    .loader-animated-24-icon__svg path:nth-child(8) {
      animation: border-jump 1000ms 1000ms infinite linear;
    }
  </style>
  <svg
    class="loader-animated-24-icon__svg"
    fill="none"
    stroke="black"
    xmlns="http://www.w3.org/2000/svg"
    width="24"
    height="24"
    fill="none"
    stroke="#000"
    stroke-linecap="round"
    stroke-linejoin="round"
    viewBox="0 0 24 24"
  >
    <path stroke-width="2" opacity="0" d="M12 6V3" />
    <path stroke-width="2" opacity="0" d="M16.25 7.75 18.4 5.6" />
    <path stroke-width="2" opacity="0" d="M18 12h3" />
    <path stroke-width="2" opacity="0" d="m16.25 16.25 2.15 2.15" />
    <path stroke-width="2" opacity="0" d="M12 18v3" />
    <path stroke-width="2" opacity="0" d="M7.75 16.25 5.6 18.4" />
    <path stroke-width="2" opacity="0" d="M6 12H3" />
    <path stroke-width="2" opacity="0" d="M7.75 7.75 5.6 5.6" />
  </svg>
  ```

- animations using SVG animations (`<animate>` or `<animateTransform>`, etc.) = **BAD**, ~100% CPU usage for one instance
- a period character (`.`) fading in and out with CSS animations and opacity = **GOOD**, undetectable CPU usage
- fading in and out opacity applied directly to an SVG element = **BAD**, ~100% CPU usage for one instance
- fading in and out opacity applied directly to SVG sub-elements (like `<path>`) = **BAD**, ~100% CPU usage for one instance
- applying CSS animations that contain rotate transforms directly to an SVG = **GOOD**, undetectable CPU usage

  ```html
  <style>
    .lines-spin svg {
      animation: spin 1s infinite linear;
    }

    @keyframes spin {
      0% {
        transform: rotate(0deg);
      }
      100% {
        transform: rotate(360deg);
      }
    }
  </style>
  <div class="lines-spin">
    <svg
      height="24"
      width="24"
      viewBox="0 0 24 24"
      stroke="black"
      stroke-linecap="round"
      stroke-linejoin="round"
    >
      <path stroke-width="2" d="M12 6V3"></path>
      <path stroke-width="2" d="M16.25 7.75 18.4 5.6"></path>
      <path stroke-width="2" d="M18 12h3"></path>
      <path stroke-width="2" d="m16.25 16.25 2.15 2.15"></path>
      <path stroke-width="2" d="M12 18v3"></path>
      <path stroke-width="2" d="M7.75 16.25 5.6 18.4"></path>
      <path stroke-width="2" d="M6 12H3"></path>
      <path stroke-width="2" d="M7.75 7.75 5.6 5.6"></path>
    </svg>
  </div>
  ```

- fading a single SVG rect in and out = **BAD**, ~100% CPU usage

  ```html
  <style>
    .rects rect {
      animation: pulse 1s infinite linear;
      stroke: red;
    }
  </style>
  <span class="rects">
    <svg height="24" width="24" viewBox="0 0 24 24" stroke="black">
      <rect width="2" height="7" />
    </svg>
  </span>
  ```

- wrapping individual SVG `<path>` elements in `<span>` elements and applying opacity animations to those spans = **GOOD**, undetectable CPU usage

  ```html
  <style>
    .individual-lines span {
      animation: pulse 1s infinite linear;
      position: absolute;
      top: 0;
      left: 0;
    }

    .individual-lines {
      height: 24px;
      width: 24px;
      /*     animation: spin 1s infinite linear; */
    }
  </style>
  <div class="individual-lines">
    <span>
      <svg
        height="24"
        width="24"
        viewBox="0 0 24 24"
        stroke="black"
        stroke-linecap="round"
        stroke-linejoin="round"
      >
        <path stroke-width="2" d="M12 6V3"></path>
      </svg>
    </span>
    <span>
      <svg
        height="24"
        width="24"
        viewBox="0 0 24 24"
        stroke="black"
        stroke-linecap="round"
        stroke-linejoin="round"
      >
        <path stroke-width="2" d="M16.25 7.75 18.4 5.6"></path>
      </svg>
    </span>
    <span>
      <svg
        height="24"
        width="24"
        viewBox="0 0 24 24"
        stroke="black"
        stroke-linecap="round"
        stroke-linejoin="round"
      >
        <path stroke-width="2" d="M18 12h3"></path>
      </svg>
    </span>
    <span>
      <svg
        height="24"
        width="24"
        viewBox="0 0 24 24"
        stroke="black"
        stroke-linecap="round"
        stroke-linejoin="round"
      >
        <path stroke-width="2" d="m16.25 16.25 2.15 2.15"></path>
      </svg>
    </span>
    <span>
      <svg
        height="24"
        width="24"
        viewBox="0 0 24 24"
        stroke="black"
        stroke-linecap="round"
        stroke-linejoin="round"
      >
        <path stroke-width="2" d="M12 18v3"></path>
      </svg>
    </span>
    <span>
      <svg
        height="24"
        width="24"
        viewBox="0 0 24 24"
        stroke="black"
        stroke-linecap="round"
        stroke-linejoin="round"
      >
        <path stroke-width="2" d="M7.75 16.25 5.6 18.4"></path>
      </svg>
    </span>
    <span>
      <svg
        height="24"
        width="24"
        viewBox="0 0 24 24"
        stroke="black"
        stroke-linecap="round"
        stroke-linejoin="round"
      >
        <path stroke-width="2" d="M6 12H3"></path>
      </svg>
    </span>
    <span>
      <svg
        height="24"
        width="24"
        viewBox="0 0 24 24"
        stroke="black"
        stroke-linecap="round"
        stroke-linejoin="round"
      >
        <path stroke-width="2" d="M7.75 7.75 5.6 5.6"></path>
      </svg>
    </span>
  </div>
  ```
