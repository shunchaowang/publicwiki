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

## Encapsulation on api layers
We'd like to have an encapsulated layers for the generic api calls. First of all let's define some basic api types
including abort function.
### We want to narrow the axios calls to our app which will be used by the use api hook, create this
`src/api/api.types.ts`. We'd like to have an api with abort functions.
```
// define the abort canceler
export type {Canceler}

// we only want these methods from axios
type AxiosMethods = Pick<AxiosInstance, 'get'|'post'|'put'|'patch'|'delete'>

// create a function signature with abort
export type WithAbortFn = AxiosMethods[keyof AxiosMethods] // this will create an object AxiosMethods['get'], etc

// 
export type ApiExecutor<T> = {
    (url: string, body: unknown, config: ApiRequestConfig): AxiosPromise<T> // 3 params executor
    (url: string, config: ApiRequestConfig): AxiosPromise<T> // 2 params executor
}

export type ApiExecutorArgs = 
    | [string, unknown, ApiRequestConfig]
    | [string, ApiRequestConfig]
    
export type ApiRequestConfig = AxiosRequestConfig & {abort?: (cancel: Canceler) => voidf} // expand the type with abort

export type ApiError = AxiosError # type alias for AxiosError
```
### Create the api layers with abort function
We want to put the codes based on convention, all source code including components, apis and pages should be in `src/`.
Create a file of `src/api/api.ts`, we want to create an abstract layer for all the apis no matter which library is used,
fetch or axios. We use axios in the code. In the apis, we want to have 5 http methods of `get`, `post`, `put`, `patch`,
and `delete`.
```
import axios, { AxiosInstance, AxiosRequestConfig, Cancel } from 'axios'
import {
  ApiError,
  ApiExecutor,
  ApiExecutorArgs,
  ApiRequestConfig,
  WithAbortFn,
} from './api.types'

const axiosParams = {
    baseURL: 
        process.env.NODE_ENV === 'development' ? 'http://localhost:8080' : '/', // set to be localhost if it's dev
}

// Create an axios instance
const axiosInstance = axios.create(axiosParams)

// if the request is caneled
export const didAbort = (error: unknown): error is Cancel & {aborted: boolean} => axios.isCancel(error)

// get cancel source
const getCancelSource = () => axios.cancelToken.source()

// define if axios call errored
export const isApiError = (error: unknown): error is ApiError => {
    return axios.isAxiosError(error)
}

// define abort function
const withAbort = <T>(fn: WithAbortFn) => {
    const executor: ApiExecutor<T> = async (...args: ApiExecutorArgs) => {
        
        const originalConfig = args[args.length - 1] as ApiRequestConfig
        const {abort, ...config} = originalConfig
        
        if (typeof abort === 'function') {
            const const {cancel, token} = getCancelSource()
            config.cancelToken = token
            abort(cancel)
        }
        
        try {
            if (args.length > 2) {
                const [url, body] = args // array destruct
                return await fn<T>(url, body, config)
            } else {
                const [url] = args
                return await fn<T>(url, config)
            }
        } catch (error) {
            console.log('api error', error)
            // add 'aborted' property to the error if the request was canceled
            if (didAbort(error)) {
                error.aborted = true
            }
            throw error
        }
    }
}

// Create the apis
We want to make this api generic, this api is created by passing an AxiosInstance to it.
const api = (axios: AxiosInstance) => {
    return {
        get: <T>(url: string, config: AxiosRequestConfig = {}) =>  withAbort<T>(axios.get)(url, config),
        post: <T>(url, body: unknown, config: AxiosRequestConfig = {}) => withAbort<T>(axios.post)(url, body, config),
        put: <T>(url, body: unknown, config: AxiosRequestConfig = {}) => withAbort<T>(axios.put)(url, body, config),
        patch: <T>(url, body: unknown, config: AxiosRequestConfig = {}) => withAbort<T>(axios.patch)(url, body, config),
        delete: <T>(url: string, config: AxiosRequestConfig = {}) =>  withAbort<T>(axios.delete)(url, config),
    }
}

export default api(axiosInstance)
```
### Extend api layers to the feature api
We want to separate the concerns between real apis and api states, we should make the feature apis focus on the apis,
regardless what the states and errors of the call. Before we move to the api states, let's create the feature apis. I'll
take an animal api as the example. Go ahead create a file `src/api/animalApi.ts`.
```
import api from './api'

const URLS = {
    fetchDogUrl: 'breeds/image/random',
    fetchCatUrl: 'images/search?format=json',
}

export type DogData = {
    message: string
    status: 'success' | 'error'
}

export const fetchDog = () => {
    return api.get<DogData>(URLS.fetchDogUrl, {
        baseURL: 'https://dog.ceo/api/',
    })
}

export type CatData = {
    breeds: []
    height: number
    width: number
    id: string
    url: string
}[]

export const fetchCat = () => {
    return api.get<CataData>(URLS.fetchCatUrl, {
        baseURL: 'https://api.thecatapi.com/v1/',
    })
}
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

4. Add a wrapper class for async # this can be removed
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
   hook file `src/api/useApiStatus.ts`.
```   
// create a type alias to store the status active status in the record
type Statuses = Record<`is${Capitalize<Lowercase<ApiStatus>>}`, boolean>

// method to capitalize the string
const capitalize = (s: string) => s.charAt(0).toUpperCase() + s.slice(1)

const prepareStatuses = (currentStatus: ApiStatus): Statuses => {
    const statuses = {} as Statuses
    
    for (const status of defaultApiStatuses) {
        const normalisedStatus = capitalize(status.toLowerCase()) // get Idle, Pending, Success, Error
        const normalisedStatusKey = `is${normalisedStatus}` as keyof Statuses
        statuses[normalisedStatusKey] = status === currentStatus
    }
    
    return statuses
}

// this will return the statuses as 
// {isIdle: true, isPending: false, isSuccess: true, isError: false}
export const useApiStatus = (currentStatus: ApiStatus = IDLE) => {
    const [status, setStatus] = useState<ApiStatus>(currentStatus)
    const statuses = useMemo(() => prepareStatuses(status), [status])
    
    return {
        status,
        setStatus,
        ...statuses
    }
}

```   
4. We also want to create another custom hook for api, we want to have a hook to wrap the api calls and return the
   response and error if there is any error occurred. Go ahead create the hook file `src/api/hooks/useApi.ts`.
```
interface UseApiConfig<T> {
    initialData?: T
}

type ApiFunction<T = unknown> = (...args: unknown[]) => T | Promise<T>

export function useApi<TData = unknown, TError = unknown>(fn: ApiFunction<TData>, config: UseApiConfig<TData> = {}) {
    const {initialData} = config
    const [data, setData] = useState<TData | undefined>(initialData)
    const [error, setError] = useState<TError | unknown>()
    const {status, setStatus, ...normalisedStatuses} = useApiStatus()
    
    const exec = async <A>(...args: A[]) => { // rest
        try {
            setStatus(PENDING)
            const data = await fn(...args) // spread 
            setData(data)
            setStatus(SUCCESS)
            return {
                data,
                error: null,
            }
        } catch (error) {
            setStatus(ERROR)
            return {
                error,
                data: null,
            }
        }
    }
    
    return {
        data,
        setData,
        status,
        setStatus,
        error,
        exec,
        ...normalizedStatuses,
    }
}
```
9. Create the feature apis with states derived from api
### Handle async calls
### Request cancellation
### Error logging
