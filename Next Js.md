# Page Navigation
There are 3 ways to navigate through pages in Next Js, by `next/Link`, by `form submission`, by `useRouter`
1. Navigate by `next/Link`
Link can be added by `<Link href='destination'>Label</Link>`
2. Navigate by `form submission`
Traditional way to submit a form to the action
3. Navigate `useRouter`
```
const router = useRouter()
router.push('/distination')
```
This is different than the `router.replace('/distination')`, `push` is to push the next link to the history stack,
`Back` will go back to the previous page; `replace` is to replace the current path with the distination, `Back` will not
go back to the previous page.

