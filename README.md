# ✅ React Task Manager - Gerenciador de Tarefas

## 🎯 Objetivo de Aprendizado
Projeto desenvolvido para estudar **React** e **gerenciamento de estado**, criando uma aplicação completa de gerenciamento de tarefas com interface moderna, responsiva e integração com API backend.

## 🛠️ Tecnologias Utilizadas
- **Framework:** React 18
- **Styling:** Tailwind CSS
- **Estado:** React Hooks (useState, useEffect, useContext)
- **Roteamento:** React Router DOM
- **HTTP Client:** Axios
- **Build Tool:** Vite
- **Deploy:** Vercel
- **Conceitos estudados:**
  - React Hooks avançados
  - Context API para estado global
  - Componentes funcionais
  - Integração com APIs REST
  - Responsive design
  - Gerenciamento de formulários

## 🚀 Demonstração
```tsx
// Context para gerenciamento de tarefas
interface TaskContextType {
  tasks: Task[];
  addTask: (task: Omit<Task, 'id'>) => Promise<void>;
  updateTask: (id: string, task: Partial<Task>) => Promise<void>;
  deleteTask: (id: string) => Promise<void>;
  loading: boolean;
}

const TaskContext = createContext<TaskContextType>({} as TaskContextType);

export const TaskProvider: React.FC<{ children: ReactNode }> = ({ children }) => {
  const [tasks, setTasks] = useState<Task[]>([]);
  const [loading, setLoading] = useState(false);

  const addTask = async (taskData: Omit<Task, 'id'>) => {
    setLoading(true);
    try {
      const response = await api.post('/tasks', taskData);
      setTasks(prev => [...prev, response.data]);
    } catch (error) {
      console.error('Error adding task:', error);
    } finally {
      setLoading(false);
    }
  };

  return (
    <TaskContext.Provider value={{ tasks, addTask, updateTask, deleteTask, loading }}>
      {children}
    </TaskContext.Provider>
  );
};

// Hook customizado
export const useTasks = () => {
  const context = useContext(TaskContext);
  if (!context) {
    throw new Error('useTasks must be used within TaskProvider');
  }
  return context;
};
```

## 💡 Principais Aprendizados

### ⚛️ React Hooks
- **useState:** Gerenciamento de estado local
- **useEffect:** Efeitos colaterais e lifecycle
- **useContext:** Estado global sem prop drilling
- **useCallback:** Otimização de performance
- **useMemo:** Memoização de valores computados

### 🎨 Interface e UX
- **Tailwind CSS:** Utility-first styling
- **Responsive Design:** Mobile-first approach
- **Component Composition:** Reutilização de componentes
- **Form Handling:** Validação e submissão

### 🔗 Integração com API
- **Axios:** Cliente HTTP para requisições
- **Error Handling:** Tratamento de erros
- **Loading States:** Estados de carregamento
- **CRUD Operations:** Create, Read, Update, Delete

## 🧠 Conceitos Técnicos Estudados

### 1. **Componente de Tarefa**
```tsx
interface TaskProps {
  task: Task;
  onUpdate: (id: string, updates: Partial<Task>) => void;
  onDelete: (id: string) => void;
}

const TaskCard: React.FC<TaskProps> = ({ task, onUpdate, onDelete }) => {
  const [isEditing, setIsEditing] = useState(false);
  const [editedTask, setEditedTask] = useState(task);

  const handleSave = () => {
    onUpdate(task.id, editedTask);
    setIsEditing(false);
  };

  const toggleStatus = () => {
    onUpdate(task.id, { 
      status: task.status === 'completed' ? 'pending' : 'completed' 
    });
  };

  return (
    <div className={`p-4 rounded-lg border ${
      task.status === 'completed' ? 'bg-green-50 border-green-200' : 'bg-white border-gray-200'
    }`}>
      {isEditing ? (
        <div className="space-y-2">
          <input
            type="text"
            value={editedTask.title}
            onChange={(e) => setEditedTask(prev => ({ ...prev, title: e.target.value }))}
            className="w-full p-2 border rounded"
          />
          <textarea
            value={editedTask.description}
            onChange={(e) => setEditedTask(prev => ({ ...prev, description: e.target.value }))}
            className="w-full p-2 border rounded"
            rows={3}
          />
          <div className="flex gap-2">
            <button onClick={handleSave} className="bg-blue-500 text-white px-3 py-1 rounded">
              Salvar
            </button>
            <button onClick={() => setIsEditing(false)} className="bg-gray-500 text-white px-3 py-1 rounded">
              Cancelar
            </button>
          </div>
        </div>
      ) : (
        <div>
          <h3 className={`font-semibold ${task.status === 'completed' ? 'line-through text-gray-500' : ''}`}>
            {task.title}
          </h3>
          <p className="text-gray-600 mt-1">{task.description}</p>
          <div className="flex justify-between items-center mt-3">
            <span className={`px-2 py-1 rounded text-xs ${
              task.status === 'completed' ? 'bg-green-100 text-green-800' : 'bg-yellow-100 text-yellow-800'
            }`}>
              {task.status === 'completed' ? 'Concluída' : 'Pendente'}
            </span>
            <div className="flex gap-2">
              <button onClick={toggleStatus} className="text-blue-500 hover:text-blue-700">
                {task.status === 'completed' ? 'Reabrir' : 'Concluir'}
              </button>
              <button onClick={() => setIsEditing(true)} className="text-green-500 hover:text-green-700">
                Editar
              </button>
              <button onClick={() => onDelete(task.id)} className="text-red-500 hover:text-red-700">
                Excluir
              </button>
            </div>
          </div>
        </div>
      )}
    </div>
  );
};
```

### 2. **Hook para API**
```tsx
const useApi = () => {
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);

  const request = useCallback(async <T>(
    method: 'GET' | 'POST' | 'PUT' | 'DELETE',
    url: string,
    data?: any
  ): Promise<T> => {
    setLoading(true);
    setError(null);
    
    try {
      const response = await api.request({
        method,
        url,
        data,
        headers: {
          'Authorization': `Bearer ${localStorage.getItem('token')}`,
          'Content-Type': 'application/json'
        }
      });
      
      return response.data;
    } catch (err: any) {
      const errorMessage = err.response?.data?.message || 'Erro na requisição';
      setError(errorMessage);
      throw new Error(errorMessage);
    } finally {
      setLoading(false);
    }
  }, []);

  return { request, loading, error };
};
```

### 3. **Filtros e Busca**
```tsx
const TaskList: React.FC = () => {
  const { tasks } = useTasks();
  const [filter, setFilter] = useState<'all' | 'pending' | 'completed'>('all');
  const [searchTerm, setSearchTerm] = useState('');

  const filteredTasks = useMemo(() => {
    return tasks.filter(task => {
      const matchesFilter = filter === 'all' || task.status === filter;
      const matchesSearch = task.title.toLowerCase().includes(searchTerm.toLowerCase()) ||
                           task.description.toLowerCase().includes(searchTerm.toLowerCase());
      return matchesFilter && matchesSearch;
    });
  }, [tasks, filter, searchTerm]);

  return (
    <div className="space-y-4">
      <div className="flex flex-col sm:flex-row gap-4">
        <input
          type="text"
          placeholder="Buscar tarefas..."
          value={searchTerm}
          onChange={(e) => setSearchTerm(e.target.value)}
          className="flex-1 p-2 border rounded-lg"
        />
        <select
          value={filter}
          onChange={(e) => setFilter(e.target.value as any)}
          className="p-2 border rounded-lg"
        >
          <option value="all">Todas</option>
          <option value="pending">Pendentes</option>
          <option value="completed">Concluídas</option>
        </select>
      </div>
      
      <div className="grid gap-4">
        {filteredTasks.map(task => (
          <TaskCard key={task.id} task={task} onUpdate={updateTask} onDelete={deleteTask} />
        ))}
      </div>
    </div>
  );
};
```

## 📁 Estrutura do Projeto
```
react-taskmanager-app/
├── src/
│   ├── components/         # Componentes reutilizáveis
│   │   ├── TaskCard.tsx
│   │   ├── TaskForm.tsx
│   │   └── TaskList.tsx
│   ├── contexts/          # Context API
│   │   └── TaskContext.tsx
│   ├── hooks/             # Custom hooks
│   │   └── useApi.ts
│   ├── pages/             # Páginas da aplicação
│   ├── services/          # Serviços de API
│   ├── types/             # TypeScript interfaces
│   └── utils/             # Utilitários
├── public/                # Assets estáticos
└── dist/                  # Build de produção
```

## 🔧 Como Executar

### Desenvolvimento Local
```bash
# Clone o repositório
git clone <repo-url>
cd react-taskmanager-app

# Instale dependências
npm install

# Configure variáveis de ambiente
echo "VITE_API_URL=http://localhost:3000" > .env

# Inicie o servidor
npm run dev
```

### Build para Produção
```bash
# Build da aplicação
npm run build

# Preview do build
npm run preview
```

## 🎯 Funcionalidades Implementadas
- ✅ **CRUD de tarefas** completo
- ✅ **Filtros** por status (todas, pendentes, concluídas)
- ✅ **Busca** por título e descrição
- ✅ **Edição inline** de tarefas
- ✅ **Toggle de status** (pendente/concluída)
- ✅ **Design responsivo** mobile-first
- ✅ **Loading states** durante requisições
- ✅ **Error handling** com feedback visual

## 🚧 Desafios Enfrentados
1. **Estado Global:** Gerenciar estado entre componentes
2. **Performance:** Otimizar re-renders desnecessários
3. **API Integration:** Sincronizar estado local com servidor
4. **Form Validation:** Validar dados antes do envio
5. **Responsive Design:** Adaptar para diferentes telas
6. **Error Handling:** Tratar falhas de rede graciosamente

## 📚 Recursos Utilizados
- [React Documentation](https://react.dev/)
- [Tailwind CSS](https://tailwindcss.com/docs)
- [React Router](https://reactrouter.com/)
- [Axios Documentation](https://axios-http.com/docs/intro)
- [Vite Guide](https://vitejs.dev/guide/)

## 📈 Próximos Passos
- [ ] Adicionar autenticação de usuários
- [ ] Implementar categorias de tarefas
- [ ] Adicionar datas de vencimento
- [ ] Criar sistema de notificações
- [ ] Implementar drag & drop para reordenar
- [ ] Adicionar modo offline com PWA

## 🔗 Projetos Relacionados
- [Nest Task Manager](../nest-taskmanager-app/) - Backend da aplicação
- [Node Task Manager](../node-task-manager/) - API alternativa
- [Front Task Manager](../front-task-manager/) - Versão HTML/CSS/JS

---

**Desenvolvido por:** Felipe Macedo  
**Contato:** contato.dev.macedo@gmail.com  
**GitHub:** [FelipeMacedo](https://github.com/felipemacedo1)  
**LinkedIn:** [felipemacedo1](https://linkedin.com/in/felipemacedo1)

> 💡 **Reflexão:** Este projeto consolidou meus conhecimentos em React e gerenciamento de estado. A experiência com Context API, hooks customizados e integração com APIs estabeleceu bases sólidas para desenvolvimento frontend moderno.