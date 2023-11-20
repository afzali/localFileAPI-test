<script>
  import {
    get,
    set,
  } from "https://unpkg.com/idb-keyval@5.0.2/dist/esm/index.js";

  let files = {};
  async function getDir() {
    try {
      const dirHandle = await window.showDirectoryPicker();

      // if (
      //   (await dirHandle.queryPermission({ mode: "readwrite" })) != "granted"
      // ) {
      //   const permission = await dirHandle.requestPermission({
      //     mode: "readwrite",
      //   });
      //   if (permission !== "granted") {
      //     throw new Error("No permission to open file");
      //   }
      // }
      await explore(dirHandle);
    } catch (err) {
      console.error(err);
    }
  }

  async function explore(dirHandle) {
    for await (const entry of dirHandle.values()) {
      console.log(entry.kind, entry.name);

      if (entry.kind === "file") {
        files[entry.name] = entry;
        // const file = await entry.getFile();
        // console.log({entry}, {file});
      } else if (entry.kind === "directory") {
        files[entry.name] = {};
      }
    }
    console.log(files);
  }
</script>

<button on:click={() => getDir()}>Get Dir</button>

<button
  on:click={async () => {
    var reader = new FileReader();
    const file = await files["info.json"].getFile();
    reader.readAsText(file, "UTF-8");
    reader.onload = function (evt) {
      console.log(evt.target.result);
    };
    reader.onload = function (evt) {
      console.log(evt.target.result);
    };
  }}>get File</button
>

<button
  on:click={async () => {
    const writableStream = await files["info.json"].createWritable();
    await writableStream.write(new Date().toString());
    await writableStream.close();
  }}>write File</button
>

<button
  on:click={async () => {
    try {
      const fileHandleOrUndefined = await get("file");
      if (fileHandleOrUndefined) {
        console.log(
          `Retrieved file handle "${fileHandleOrUndefined.name}" from IndexedDB.`
        );
        const writableStream = await files["info.json"].createWritable();
        await writableStream.write("from storage");
        await writableStream.close();
        return;
      }
      // This always returns an array, but we just need the first entry.

      await set("file", files["info.json"]);
      console.log(`Stored file handle for "${files["info.json"].name}" in IndexedDB.`);
    } catch (error) {
      alert(error.name, error.message);
    }
  }}>save Files in storage</button
>


<a href="#cccc">change rout</a>