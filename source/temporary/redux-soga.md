

## Error: call: argument fn is undefined (dva)


service:
    export async function getTableList(params) {
        return request('menu/listAllForEntry', { body: params })
    }


model:

    import { getTablelist } from '../../services/extraRecord'
    ... 略
    effects: {
            *getTableList({ payload }, { call, put }) {
                let params = { ...QUERY, ...payload }
                const { CODE, DATA, MSG } = yield call(getTablelist, params)    // 问题所在
            },
    }


trigger:

    dispatch({ type: 'getTableList' })


solve:
    大小写问题