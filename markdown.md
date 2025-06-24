# Ejemplos de Markdown

**Esto es un texto en negrita**

*Esto es un texto en cursiva*

## Esto es un título de nivel 2

Aquí va algo de código JavaScript

```js
const isLeapYear = year => new Date(year, 1, 29).getMonth() === 1;
```

Y algo de de bash:

```sh
git branch -m <old-name> <new-name>
git push origin --delete <old-name>
git checkout <new-name>
git push origin -u <new-name>
```

O algo sin especificar el lenguaje

```
func IntRange(f, t, s int) []int {
	arr := make([]int, (t-f+1)/s)
	for i := range arr {
		arr[i] = i*s + f
	}
	return arr
}
```
