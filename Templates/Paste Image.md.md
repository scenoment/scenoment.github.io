<%*
const title = tp.file.title;
const vault = app.vault;
const attachmentsPath = "_attachments"; // <-- Obsidian 설정에 맞춰 수정 가능
const targetImageFolder = `images/${title}`;

const file = tp.file.find_tfile(title);
let content = await vault.read(file);

// ![[이미지.png]] 형식 찾기
const matches = [...content.matchAll(/!\[\[([^\]]+\.(png|jpg|jpeg|gif))\]\]/g)];

for (const match of matches) {
  const imageName = match[1];
  const fromPath = `${attachmentsPath}/${imageName}`;
  const toPath = `${targetImageFolder}/${imageName}`;

  const imageFile = vault.getAbstractFileByPath(fromPath);
  if (!imageFile) {
    console.warn(`파일을 찾을 수 없음: ${fromPath}`);
    continue;
  }

  // 대상 폴더 없으면 만들기
  const folderExists = await vault.adapter.exists(targetImageFolder);
  if (!folderExists) {
    await vault.createFolder(targetImageFolder);
  }

  // 이동
  await vault.rename(imageFile, toPath);

  // 링크 교체
  content = content.replace(match[0], `![image](../${toPath})`);
}

// 문서 업데이트
await vault.modify(file, content);
%>
