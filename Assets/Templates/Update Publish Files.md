#IgnorePublish 
<%*
const dv = app.plugins.plugins["dataview"].api;
const openPublishPanel = app.commands.commands["publish:view-changes"].callback;

// Add as many filenames and queries as you'd like!
const fileAndQuery = new Map([
  [
    "Last Modified",
    'TABLE file.mtime AS "Last Modified" FROM "" SORT file.mtime Asc LIMIT 5',,
  ],
  [
    "Added Files",
    'TABLE WITHOUT ID file.link AS "NOTE", dateformat(file.ctime, "DD") AS ADDED FROM "" WHERE file.cday SORT file.ctime Asc LIMIT 5',
  ],
  [
    "Adventures-1",
    'TABLE WITHOUT ID file.link AS "Adventures" FROM #adventure AND -#IgnorePublish WHERE file.cday SORT file.ctime Asc LIMIT 5',
  ],
  [
    "Character Sheet-1",#IgnorePublish 



    'TABLE WITHOUT ID file.link AS "Character Sheet" FROM #character-sheet AND -#IgnorePublish WHERE file.cday SORT file.ctime Asc LIMIT 5',  
  ],

  [
    "Adventures-character-sheets",
    'TABLE WITHOUT ID file.link AS "Adventure Character Sheet" FROM #Adventure-character-sheets AND -#IgnorePublish WHERE file.cday SORT file.ctime Asc LIMIT 5',
  ],
]);

await fileAndQuery.forEach(async (query, filename) => {
  if (!tp.file.find_tfile(filename)) {
    await tp.file.create_new("", filename);
    new Notice(`Created ${filename}.`);
  }
  const tFile = tp.file.find_tfile(filename);
  const queryOutput = await dv.queryMarkdown(query);
  const fileContent = `%% #Ignore update via "Update Publish Files" template %% \n\n${queryOutput.value}`;
  try {
    await app.vault.modify(tFile, fileContent);
    new Notice(`Updated ${tFile.basename}.`);
  } catch (error) {
    new Notice("⚠️ ERROR updating! Check console. Skipped file: " + filename , 0);
  }
});
openPublishPanel();
%>

