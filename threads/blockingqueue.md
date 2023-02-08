    public class BlockingQueue<T> {

        private Queue<T> queue = new LinkedList<T>();
        private int capacity;

        public BlockingQueue(int capacity) {
            this.capacity = capacity;
        }

        public synchronized void put(T element) throws InterruptedException {
            while(queue.size() == capacity) {
                wait();
            }

            queue.add(element);
            notify(); // notifyAll() for multiple producer/consumer threads
        }

        public synchronized T take() throws InterruptedException {
            while(queue.isEmpty()) {
                wait();
            }

            T item = queue.remove();
            notify(); // notifyAll() for multiple producer/consumer threads
            return item;
        }
    }
