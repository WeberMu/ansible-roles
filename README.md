# Ansible Role: MongoDB Sharded Cluster

该仓库提供了 `mongodb_sharded_cluster` 角色，用于一键部署 MongoDB 分片集群，包含：

- Config Server 副本集
- 多个 Shard 副本集
- Mongos 路由节点
- 自动初始化 `rs.initiate`、`sh.addShard`

> 适配 Ubuntu 22.04（`jammy`）+ MongoDB 7.0（可改变量）。

## 目录结构

```text
roles/mongodb_sharded_cluster/
  defaults/main.yml
  handlers/main.yml
  meta/main.yml
  tasks/
    main.yml
    setup.yml
    init_config_replset.yml
    init_shards.yml
    add_shards.yml
  templates/
    mongod.conf.j2
    mongos.conf.j2
```

## 快速使用

### 1) 准备 inventory

示例见 `examples/inventory.ini`。

### 2) 准备变量

可把变量写在 `group_vars/all.yml`（示例见 `examples/group_vars_all.yml`），至少要改：

- `mongodb_keyfile_content`
- `mongodb_admin_password`

### 3) 执行 playbook

```bash
ansible-playbook -i examples/inventory.ini examples/site.yml
```

## 关键变量

| 变量 | 默认值 | 说明 |
|---|---|---|
| `mongodb_version` | `7.0` | MongoDB 版本 |
| `mongodb_repo_dist` | `jammy` | Ubuntu 发行版代号 |
| `mongodb_config_servers_group` | `mongo_configsvr` | Config Server 组名 |
| `mongodb_mongos_group` | `mongo_mongos` | Mongos 组名 |
| `mongodb_shards` | 见 defaults | 分片列表（每个元素包含 `replset` 和 `group`） |
| `mongodb_port_mongod` | `27017` | mongod 端口 |
| `mongodb_port_mongos` | `27018` | mongos 端口 |

## 注意事项

1. 该角色假定 inventory 主机名可互相解析（或写成 IP）。
2. 生产环境建议配 TLS、监控、备份，并将密码与 keyfile 放入 Ansible Vault。
3. 首次初始化后，角色是幂等的；再次执行会跳过已初始化逻辑。
