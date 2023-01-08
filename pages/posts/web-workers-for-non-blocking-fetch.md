---
title: Use web workers for non-blocking fetch calls
date: 2023/1/06
description: A client-side ecommerce wizard required posting some data without holding up purchase.
tag: web workers,javascript
author: Andy Merhaut
---

# Use web workers for non-blocking fetch calls

```
function CreateWorkerFromFunction(fn) {
    const blob = new Blob([`(${fn.toString()})()`], { type: 'text/javascript' });
    const url = URL.createObjectURL(blob);
    return new Worker(url);
}

const POST_TIMEOUT_SECONDS = 5;
const worker = CreateWorkerFromFunction(function() {
    this.onmessage = (e) => {
        const request = e.data;
        const controller = new AbortController();
        setTimeout(() => controller.abort(), request.timeout * 1000);

        request.payload.signal = controller.signal;

        fetch(request.url, request.payload)
            .then(() => {
                postMessage('Success');
            })
            .catch((error) => {
                postMessage(`Error: ${error.name}`);
            });
    };
});
export async function postData(url = '', data = {}, spawnWorker = false) {
    const request = {
        url: url,
        timeout: POST_TIMEOUT_SECONDS,
        payload: {
            method: 'POST',
            mode: 'same-origin',
            cache: 'no-cache',
            credentials: 'same-origin',
            headers: {
                'Content-Type': 'application/json'
            },
            redirect: 'error',
            referrerPolicy: 'no-referrer',
            body: JSON.stringify(data)
        }
    };

    if(spawnWorker) {
        if(!url.startsWith(location.protocol)) {
            request.url = `${location.protocol}${location.hostname}${url}`;
        }

        worker.postMessage(request);

        worker.onmessage = (e) => {
            console.debug('Message received from worker', e.data);
        };
        return;
    }

    const controller = new AbortController();
    setTimeout(() => controller.abort(), POST_TIMEOUT_SECONDS * 1000);

    request.payload.signal = controller.signal;
    const response = await window.fetch(request.url, request.payload);
    return response.json();
}

export async function getData(url = '') {
    const response = await window.fetch(url, {
        method: 'GET',
        mode: 'same-origin',
        cache: 'no-cache',
        credentials: 'same-origin',
        headers: {
            'Content-Type': 'application/json'
        },
        redirect: 'error',
        referrerPolicy: 'no-referrer'
    });


```