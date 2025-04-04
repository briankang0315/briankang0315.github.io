---
title: "과제 배포 하는법 항해99"
date: 2025-04-03 22:00:00 +1100
categories: [ Programming, Bootcamp ]
tags: [ 항해99, Front end, 항해플러스 ]
author: <author_id>   
---

pnpm install gh-pages --save-dev 으로 package 다운 받고 

package.json에
```jsx
 "deploy":"gh-pages -d dist",
```

createRouter.js를 이렇게 바꿔주고
```jsx
import {createObserver} from "./createObserver";

const BASE_URL = import.meta.env.VITE_BASE_URL || "";

export const createRouter = (routes) => {
  const {subscribe, notify} = createObserver();

  const getPath = () => window.location.pathname.replace(BASE_URL, "");

  const getTarget = () => routes[getPath()];

  const push = (path) => {
    window.history.pushState(null, null, `${BASE_URL}${path}`);
    notify();
  };

  window.addEventListener("popstate", () => notify());

  return {
    get path() {
      return getPath();
    },
    push,
    subscribe,
    getTarget,
  };
};

```

vite.config.js를 이걸로 바꿔주고
```jsx
import { defineConfig as defineTestConfig, mergeConfig } from "vitest/config";
import { defineConfig, loadEnv } from "vite";
import { resolve } from "path";

export default ({ mode }) => {
  const env = loadEnv(mode, process.cwd(), "");

  return mergeConfig(
    defineConfig({
      esbuild: {
        jsxFactory: "createVNode",
      },
      optimizeDeps: {
        esbuildOptions: {
          jsx: "transform",
          jsxFactory: "createVNode",
        },
      },
    }),
    defineTestConfig({
      test: {
        globals: true,
        environment: "jsdom",
        setupFiles: "./src/setupTests.js",
        exclude: ["**/e2e/**", "**/*.e2e.spec.js", "**/node_modules/**"],
      },
      base: env.VITE_BASE_URL,
      build: {
        rollupOptions: {
          input: {
            main: resolve(__dirname, "index.html"),
            hash: resolve(__dirname, "index.hash.html"),
          },
        },
      },
    }),
  );
};
```


github pages 가서 확인.. 

배포 확인 완료..!
