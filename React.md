## Project architecture
## Hooks
## Api
When we work with api layers, we want to abstract server communication into a single api layers, this will provide
reusable components across pages/modules/application. To accomplish this, we put an api layer between server and client
components.
                     **Server**
                       |
                   **Api Layer**
                       |
                 **Feature Apis**
                /      |      \
               /       |       \
            /          |        \
**Component One** **Component Two** **Component Three**

### Encapsulation on api layers
We want to put the codes based on convention, all source code including components, apis and pages should be in `src/`.
Create a file of `src/api/api.ts`, we want to create an abstract layer for all the apis no matter which library is used,
fetch or axios. We use axios in the code. In the apis, we want to have 5 http methods of `get`, `post`, `put`, `patch`,
and `delete`.
- Create a params for axios instance
Axios needs a base url to inintialize the instance, create this object
```
const axiosParams = {
    baseURL: 
        process.env.NODE_ENV === 'development' ? 'http://localhost:8080' : '/', // set to be localhost if it's dev
}
```
- Create an axios instance
`const axiosInstance = axios.create(axiosParams)`
- Create the apis
We want to make this api generic, this api is created by passing an AxiosInstance to it.
```
const api = (axios: AxiosInstance) => {
    return {
        get: <T>(url: string, config: AxiosRequestConfig = {}) =>  axios.get<T>(url, config),
        post: <T>(url, body: unknown, config: AxiosRequestConfig = {}) => axios.post<T>(url, body, config),
        put: <T>(url, body: unknown, config: AxiosRequestConfig = {}) => axios.put<T>(url, body, config),
        patch: <T>(url, body: unknown, config: AxiosRequestConfig = {}) => axios.patch<T>(url, body, config),
        delete: <T>(url: string, config: AxiosRequestConfig = {}) =>  axios.delete<T>(url, config),
    }
}

export default api(axiosInstance)
```
### Handle api states
Each api call will have different states, like idle, pending if the api takes time to be available, success when the api
calls are successful, error when the call fails. We want to put these states in the component as well. Unlike the api of
common utilities, api states should be put into the **feature apis** components.
```
IDLE - the starting point
PENDING - An action is being performed
SUCCESS - An action finished successfully
ERROR - An action finished with an error 
```
1. Define a constant class to avoid string as the api states
create the file `src/api/constants/apiStatus.ts` with the conent
```
export type ApiStatus = | 'IDLE' | 'PENDING' | 'SUCCESS' | 'ERROR' // this is the type for api status

// create the constants for api status, these will be used by the components
export const IDLE:ApiStatus = 'IDLE'
export const PENDING:ApiStatus = 'PENDING'
export const SUCCESS:ApiStatus = 'SUCCESS'
export const ERROR:ApiStatus = 'ERROR'
```
2. Add a wrapper class for async
All aync calls need the try/catch, which creates the duplicated codes, we can imporve the apis to be wrapped in a common
components. Create `src/helpers/withAsync.ts` file.
```
// async functions returns either object or Promise containing the content and the error
//// define the async function
type WithAsyncFn<T = unknown> = () => T | Promise<T> // the generic function type
//// define the async function return
type WithAsyncReturn<TData, TError> = Promise<{
    response: TData | null
    error: TError | unknown
    }>
//// define the async wrapper
export async function withAsync<TData = unknown, TError = unknown>(
    fn: WithAsyncFn<TData>
): WithAsyncReturn<TData, TError> {
    try {
        if (typeof fn !== 'function') {
            throw new Error('The first argument must be a function')
        }
        const response = await fn()
        return {
            response,
            error: null
        }
    } catch (error) {
        return {
            error,
            response: null
        }
    } 
}
```
3. Improve the code to use a custom hook for api status. We want to have a hook just have IDLE, PENDING, etc. Something
   like `const {status, setStatus, isIdle, isPending, isError, isSuccess} = useApiStatus(IDLE)`. Go ahead create the
   hook file `src/api/hooks/useApiStatus.ts`.
```   

```   
5. Create the feature apis with states derived from api
### Handle async calls
### Request cancellation
### Error logging
