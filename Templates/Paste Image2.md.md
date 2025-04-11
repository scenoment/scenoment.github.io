<%*
const title = tp.file.title.replace(/\s+/g, '-'); // 공백 제거된 제목
const vault = app.vault;
const attachmentsPath = "_attachments";
const targetImageFolder = `images/${title}`;

const file = tp.file.find_tfile(tp.file.title);
let content = await vault.read(file);

// ![[파일명]] 찾기
const matches = [...content.matchAll(/!\[\[([^\]]+\.(png|jpg|jpeg|gif))\]\]/g)];

for (const match of matches) {
  const originalImageName = match[1];
  const imageName = originalImageName.replace(/\s+/g, '_'); // 파일명 공백 제거
  const fromPath = `${attachmentsPath}/${originalImageName}`;
  const toPath = `${targetImageFolder}/${imageName}`;

  const imageFile = vault.getAbstractFileByPath(fromPath);
  if (!imageFile) continue;

  const folderExists = await vault.adapter.exists(targetImageFolder);
  if (!folderExists) await vault.createFolder(targetImageFolder);

  await vault.rename(imageFile, toPath);

  const encodedPath = toPath.replace(/\s/g, '%20');
  const link = `\n\n![image](/${encodedPath})\n`; // 줄바꿈 포함 링크 생성

  content = content.replace(match[0], link);
}

await vault.modify(file, content);
%>
