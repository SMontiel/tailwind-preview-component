<template>
  <div class="h-full">

    <div class="p-8 bg-gray-700 min-h-screen flex font-sans">
      <div class="bg-gray-500 w-full rounded-t shadow-md flex flex-col">
        <div class="bg-white p-4 rounded-t flex justify-between items-center">
          <p class="text-gray-900 tracking-wide">Tailwind preview component</p>
          <div class="flex">
            <button v-on:click="changeView" class="bg-blue-200 text-purple-800 hover:bg-blue-200 px-2 py-1 rounded-lg tracking-wide">{{ buttonText }}</button>
          </div>
        </div>

        <div v-bind:class="{ 'hidden': !isPreview}" class="relative pr-4 h-full" id="resizable">
          <iframe class="w-full h-full bg-white" id="iframe"></iframe>

          <div id="handle" class="absolute inset-y-0 right-0 w-4 flex items-center bg-gray-400">
            <svg class="h-4 w-4 pointer-events-none" viewBox="0 0 24 24">
              <path fill="#555" d="M8 5h2v14H8zM14 5h2v14h-2z" />
            </svg>
          </div>
        </div>
        <div v-bind:class="{ 'hidden': isPreview}" class="h-full bg-gray-200 p-4">
          <textarea v-model="code" class="form-textarea block w-full h-full" rows="3" placeholder="Write some code!"></textarea>
        </div>

      </div>
    </div>

  </div>
</template>

<script>
  import interact from "interactjs";

  export default {
    name: "TailwindPreviewComponent",
    props: {
      host: String
    },
    data() {
      return {
        buttonText: 'Code',
        code: `<div class="bg-gray-200 h-full p-4">
    <div class="md:flex bg-white rounded-lg p-6 shadow">
        <img class="h-16 w-16 md:h-24 md:w-24 rounded-full mx-auto md:mx-0 md:mr-6" src="${this.host}images/logo.png">
        <div class="text-center md:text-left">
            <h2 class="text-lg">Erin Lindford</h2>
            <div class="text-purple-500">Customer Support</div>
            <div class="text-gray-600">erinlindford@example.com</div>
            <div class="text-gray-600">(555) 765-4321</div>
        </div>
    </div>
</div>`,
        isPreview: true,
      };
    },

    methods: {
      changeView: function () {
        this.isPreview = !this.isPreview;
        this.buttonText = this.isPreview ? 'Code' : 'Preview';
        const url = this.getGeneratedPageURL({
          host: this.host,
          html: this.code
        });
        const iframe = document.querySelector('#iframe');
        iframe.src = url;
      },
      getGeneratedPageURL: ({ host, html }) => {
        const getBlobURL = (code, type) => {
          const blob = new Blob([code], { type });
          return URL.createObjectURL(blob)
        };
        const source = `
                    <html>
                        <head>
                            <link rel="stylesheet" type="text/css" href="${host}css/tailwind.min.css" />
                        </head>
                        <body>
                            ${html || ''}
                        </body>
                    </html>`;
        return getBlobURL(source, 'text/html')
      },
      escapeKeyListener: function(e) {
        if (e.key === 'Escape') {
          this.changeView();
        }
      }
    },
    created() {
      document.addEventListener('keydown', this.escapeKeyListener);
    },
    destroyed() {
      document.removeEventListener('keydown', this.escapeKeyListener);
    },
    mounted() {
      interact(document.getElementById('resizable'))
              .resizable({
                invert: 'none',
                edges: {
                  top: false,
                  left: false,
                  bottom: false,
                  right: document.getElementById('handle'),
                },
                restrictSize: {
                  min: { width: 336 },
                  max: 'parent'
                }})
              .on('resizemove', event => {
                Object.assign(event.target.style, {
                  width: `${event.rect.width}px`,
                })
              });
      const url = this.getGeneratedPageURL({
        host: this.host,
        html: this.code
      });
      const iframe = document.querySelector('#iframe');
      iframe.src = url;
    }
  }
</script>

<style scoped>
  button:focus{
    outline: none;
  }
</style>
