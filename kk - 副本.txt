// ==UserScript==
// @name         kk下载
// @namespace    http://tampermonkey.net/
// @version      0.1
// @description  try to take over the world!
// @author       aefASAA 脚本处于测试阶段，功能并不稳定，但绝大多数网站可以正常运行
// @match        *://*/*
// @grant        none
// @run-at       document-start
// ==/UserScript==

(function () {
   'use strict';
   console.log(`Unlimited_downloader: begin......${location.href}`);

   window.autoDownload = 1;
   window.isComplete = 0;
   window.audio = [];
   window.video = [];
   window.downloadAll = 0;
   window.quickPlay = 1.0;

   const originalEndOfStream = window.MediaSource.prototype.endOfStream;
   const originalAddSourceBuffer = window.MediaSource.prototype.addSourceBuffer;

   window.MediaSource.prototype.endOfStream = function () {
      window.isComplete = 1;
      return originalEndOfStream.apply(this, arguments);
   }

   window.MediaSource.prototype.addSourceBuffer = function (mime) {
      console.log("MediaSource.addSourceBuffer ", mime);
      if (mime.toString().indexOf('audio') !== -1) {
         window.audio = [];
         console.log('audio array cleared.');
      } else if (mime.toString().indexOf('video') !== -1) {
         window.video = [];
         console.log('video array cleared.');
      }
      let sourceBuffer = originalAddSourceBuffer.call(this, mime);
      const originalAppend = sourceBuffer.appendBuffer;
      sourceBuffer.appendBuffer = function (buffer) {
         console.log(mime, buffer);
         if (mime.toString().indexOf('audio') !== -1) {
            window.audio.push(buffer);
         } else if (mime.toString().indexOf('video') !== -1) {
            window.video.push(buffer);
         }
         originalAppend.call(this, buffer);
      }
      return sourceBuffer;
   }

   function download() {
      try {
         let a = document.createElement('a');
         a.href = window.URL.createObjectURL(new Blob(window.audio));
         a.download = 'audio_' + document.title + '.mp4';
         a.click();
         a.href = window.URL.createObjectURL(new Blob(window.video));
         a.download = 'video_' + document.title + '.mp4';
         a.click();
         window.downloadAll = 0;
         window.isComplete = 0;
      } catch (error) {
         console.error('Error during download:', error);
      }
   }

   function checkAndDownload() {
      if (window.downloadAll === 1 || window.isComplete === 1) {
         download();
      }
   }

   setInterval(checkAndDownload, 2000);

   if (window.autoDownload === 1) {
      setInterval(checkAndDownload, 2000);
   }

   (function (that) {
      let removeSandboxInterval = setInterval(() => {
         if (that.document.querySelectorAll('iframe')[0] !== undefined) {
            that.document.querySelectorAll('iframe').forEach((v, i, a) => {
               let ifr = v;
               ifr.removeAttribute('sandbox');
               const parentElem = that.document.querySelectorAll('iframe')[i].parentElement;
               a[i].remove();
               parentElem.appendChild(ifr);
            });
            clearInterval(removeSandboxInterval);
         }
      }, 1000);
   })(window);
})();