<%*
let content = await app.vault.read(tp.file.find_tfile(tp.file.title));

// ![[파일명.png]] 찾기
const regex = /!\[\[([^\]]+\.(png|jpg|jpeg|gif))\]\]/g;
let matches = [...content.matchAll(regex)];

for (let match of matches) {
  const fileName = match[1];
  const newMarkdown = `![image](../images/${fileName})`;
  content = content.replace(match[0], newMarkdown);
}

await app.vault.modify(tp.file.find_tfile(tp.file.title), content);
%>
